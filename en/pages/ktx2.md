# Using KTX2

On Safari iOS your page may refresh several times then give up because of memory constraint because you're using too many 4k or 8k jpg/png textures. The gpu memory constraint applies to all images that are uploaded to the gpu, with 3d models, or aframe `material` component that references an image.
## Optimizing glTF models

You can use the `gltf-transform optimize` command to optimize glTF models, by default it converts the textures to 2k ktx2 (etc1s / uastc) saving a lot of gpu memory and also applies lots of other steps.

An example of usage:

```sh
gltf-transform optimize car.glb car-optimized.glb --simplify false
```

It optimize the `car.glb` model generating a new `car-optimized.glb` file, disabling the `simplify` step that didn't do great on this model. See options in [gltf-transform cli documentation](https://gltf-transform.dev/cli)

For use the new model with aframe, you will need to have the draco decoder path and basis transcoder path correctly configured like this:

```js
<a-scene gltf-model="dracoDecoderPath:https://www.gstatic.com/draco/versioned/decoders/1.5.6/;basisTranscoderPath:https://cdn.jsdelivr.net/npm/three@0.154.0/examples/jsm/libs/basis/">
```

Check the [gltf-model aframe documentation](https://github.com/aframevr/aframe/blob/master/docs/components/gltf-model.md#using-compression) for up to date versions to use here.

You can look at the memory usage of your model with https://gltf.report/ site.
Vincent: The memory reduction is insane, for a scene that I couldn't load on Quest 1, it was vram 1.1 GB, with optimization it shrinked to vram 220 MB.
# Converting png to ktx2

Vincent: Also I did recently an experience with 8192x8192 360 stereo image generated with AI models, that I converted to ktx2 with the `basisu` command and loaded with `KTX2Loader`. The images loads smoothly on Quest 1.

Maybe some of you remember the article [Using Basis Textures in Three.js](https://medium.com/samsung-internet-dev/using-basis-textures-in-three-js-6eb7e104447d) written by Ada.
It was with the `.basis` extension, now the standard is `.ktx2`.

To install the `basisu` command on Ubuntu:

```sh
sudo apt-get install git build-essential cmake make
git clone https://github.com/BinomialLLC/basis_universal.git
cd basis_universal
cmake CMakeLists.txt
make
```

To convert an image:

```qh
./bin/basisu -y_flip -ktx2 image.png
```

The command creates an `image.ktx2` file that you can preview with https://sandbox.babylonjs.com/

To load the image in aframe, that's similar to the Ada's article, but with `ktxLoader`:

```js
const ktxLoader = AFRAME.scenes[0].systems['gltf-model'].getKTX2Loader();
ktxLoader.load('/image.ktx2',
  (texture) => {
    texture.encoding = THREE.sRGBEncoding;
    material.map = texture;
    material.needsUpdate = true;
  }, undefined, (error) => {
    console.error(error);
  }
);
```
