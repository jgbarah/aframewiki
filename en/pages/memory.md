# Destroying a scene or switching scene without memory leak

If you destroy your scene, you'll surely see a memory leak, it's because ThreeJS and currently aframe doesn't do anything about cleaning caches, closing and disposing of textures, materials, geometries (in aframe 1.3.0). You need to do all that by yourself.
This issue was asked in this [stackoverflow question](https://stackoverflow.com/questions/54930875/aframejs-how-to-completely-destroy-a-scene) and also discussed in [aframe issue #3117](https://github.com/aframevr/aframe/issues/3137).

For the animation loop part described in this stackoverflow question, this is still true in aframe 1.3.0, the loop is continuing to execute after you deleted the scene but doesn't do really much anymore. A PR is pending to fix that https://github.com/aframevr/aframe/pull/5112
In the meantime, you may use the following to properly stop the animation loop:

```
const scene = document.querySelector('a-scene');
  if (scene) {
    scene.parentNode.removeChild(scene);
    if (scene.renderer) {
      // aframe already do scene.renderer.xr.dispose(); when the scene is
      // detached but this doesn't stop properly the animation loop,
      // renderer.dispose() calls xr.dispose() and animation.stop()
      scene.renderer.dispose();
    }
  }
```

In three r141, in GLTFParser (used by GLTFLoader), we have the following code:

```
            const isSafari = /^((?!chrome|android).)*safari/i.test( navigator.userAgent ) === true;
			const isFirefox = navigator.userAgent.indexOf( 'Firefox' ) > - 1;
			const firefoxVersion = isFirefox ? navigator.userAgent.match( /Firefox\/([0-9]+)\./ )[ 1 ] : - 1;

			if ( typeof createImageBitmap === 'undefined' || isSafari || isFirefox && firefoxVersion < 98 ) {

				this.textureLoader = new THREE.TextureLoader( this.options.manager );

			} else {

				this.textureLoader = new THREE.ImageBitmapLoader( this.options.manager );

			}
```

meaning it's using latest [createImageBitmap](https://developer.mozilla.org/en-US/docs/Web/API/createImageBitmap) API available in chromium based browsers and Firefox 98 through the `THREE.ImageBitmapLoader` otherwise it's using the `THREE.TextureLoader` that will use `ImageLoader` to create a `img` element and setting the `src` attribute. This `img` element is what will be used for the `texture.image`.

One thing to know is that those loaders are all using `THREE.Cache` when enabled, and in aframe we have it [enabled by default](https://github.com/aframevr/aframe/blob/6fa0ea9407073eb7af51979092c19c8e7d68aad1/src/lib/three.js#L16-L19). You can decide to disable the cache in your project by setting `THREE.Cache.enabled = false` but it may not be a good idea if you load several times the same texture.

For example `ImageBitmapLoader` is using `fetch` the, `res.blob()` and `window.createImageBitmap(blob)` and cache this result.
`THREE.Cache` is used in `ImageLoader` and `ImageBitmapLoader`, so it's still useful to enable the cache to not create again an img element in the case of `ImageLoader`, and to avoid redoing a `fetch` (from disk cache), followed by `res.blob()` and `window.createImageBitmap(blob)` in the case of `ImageBitmapLoader`.

You can see all cached textures in `THREE.Cache.files` array.
So if you want to free some memory, you may want to clear the cache: `THREE.Cache.clear()` just after you loaded your scene with all glb and textures, depending of your needs.

Also `GLTFLoader` is not calling `close` on the `ImageBitmap` that are created
https://github.com/mrdoob/three.js/issues/23953
https://github.com/google/model-viewer/pull/3399/files
so better close them when you're disposing the materials (see `disposeTextures` function below).

Also the renderer may still keep a reference to removed entities preventing the garbage collector to free the memory right away, because it maintain a list of objects it needs to render.
It's safe to dispose this list (`this.el.sceneEl.renderer.renderLists.dispose();` line below), it will be recreated automatically on next render if needed.

Here is an example of overriding the gltf-model component to dispose all resources in `remove()`:

```
/* global AFRAME, THREE */
function disposeTextures(material) {
  // Explicitly dispose any textures assigned to this material
  for (const propertyName in material) {
    const texture = material[propertyName];
    if (texture instanceof THREE.Texture) {
      const image = texture.source.data;
      if (image instanceof ImageBitmap) {
        image.close && image.close();
      }
      texture.dispose();
    }
  }
}

function disposeNode(node) {
  if (node instanceof THREE.Mesh) {
    const geometry = node.geometry;
    if (geometry) {
      geometry.dispose();
    }

    const material = node.material;
    if (material) {
      if (Array.isArray(material)) {
        for (let i = 0, l = material.length; i < l; i++) {
          const m = material[i];
          disposeTextures(m);
          m.dispose();
        }
      } else {
        disposeTextures(material);
        material.dispose(); // disposes any programs associated with the material
      }
    }
  }
}

var warn = AFRAME.utils.debug("components:gltf-model:warn");

/**
 * glTF model loader.
 */
delete AFRAME.components["gltf-model"];
AFRAME.registerComponent("gltf-model", {
  schema: { type: "model" },

  init: function () {
    var self = this;
    var dracoLoader = this.el.sceneEl.systems["gltf-model"].getDRACOLoader();
    var meshoptDecoder = this.el.sceneEl.systems["gltf-model"].getMeshoptDecoder();
    this.model = null;
    this.loader = new THREE.GLTFLoader();
    if (dracoLoader) {
      this.loader.setDRACOLoader(dracoLoader);
    }
    if (meshoptDecoder) {
      this.ready = meshoptDecoder.then(function (meshoptDecoder) {
        self.loader.setMeshoptDecoder(meshoptDecoder);
      });
    } else {
      this.ready = Promise.resolve();
    }
  },

  update: function () {
    var self = this;
    var el = this.el;
    var src = this.data;

    if (!src) {
      return;
    }

    this.remove();

    this.ready.then(function () {
      self.loader.load(
        src,
        function gltfLoaded(gltfModel) {
          self.model = gltfModel.scene || gltfModel.scenes[0];
          self.model.animations = gltfModel.animations;
          el.setObject3D("mesh", self.model);
          el.emit("model-loaded", { format: "gltf", model: self.model });
        },
        undefined /* onProgress */,
        function gltfFailed(error) {
          var message = error && error.message ? error.message : "Failed to load glTF model";
          warn(message);
          el.emit("model-error", { format: "gltf", src: src });
        }
      );
    });
  },

  remove: function () {
    if (!this.model) {
      return;
    }
    this.el.removeObject3D("mesh");
    // New code to remove all resources
    this.model.traverse(disposeNode);
    this.model = null;

    THREE.Cache.clear();
    // Empty renderLists to remove references to removed objects for garbage collection
    this.el.sceneEl.renderer.renderLists.dispose();
  },
});
```
