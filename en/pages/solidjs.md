# Use SolidJS with aframe

The following are notes from Vincent Fretin.

You can use [SolidJS](https://www.solidjs.com) (without SolidStart for now) with aframe and networked-aframe.
You need to create `index.html` and put script tags (aframe and other components) and template tags (for networked-aframe) in it.
You can render your scene with a SolidJS component. The only gotcha is to use `attr:` on your components for them to be reactive. This applies to any web components, see the [mention in SolidJS documentation](https://www.solidjs.com/docs/latest/api#attr___).

In the root of your app, render scene like that:

```js
<Scene scene={scene()} />
```

`scene()` is a signal that is an object with sceneName and other things specific to my app.

```js
const Scene: Component<Props> = (props) => {
  onCleanup(() => {
    // Remove class that was added by aframe, otherwise we can't scroll.
    document.querySelector("html").classList.remove("a-fullscreen");
  });
  return (
    <Portal>
      <a-scene>
        <a-entity
          attr:scene-switcher={props.scene.sceneName}
          shadow="cast:true;receive:true;"
        ></a-entity>
      </a-scene>
    </Portal>
  );
};
```

I'm still using webpack with SolidJS, no ssr.
I'm trying to convert my project to SolidStart so to vite, but I struggle to import dynamically my scripts and add template tags for naf. (2023-04-13)

I'm also patching `node_modules/solid-js/types/jsx.d.ts`
to add aframe primitives in the existing `IntrinsicElements` interface like this:

```
  interface IntrinsicElements {
    "a-scene": any;
    "a-entity": any;
    "a-assets": any;
    "a-asset-item": any;
    "a-mixin": any;
    "a-sphere": any;
    "a-torus": any;
    "a-gltf-model": any;
    "a-light": any;
    a: AnchorHTMLAttributes<HTMLAnchorElement>;
```

That's not great for sure. When I tried to add that in a new d.ts file in my project I didn't manage to make it work (with solid-js 1.6.2).
