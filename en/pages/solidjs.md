# Use SolidJS with aframe

The following are notes from Vincent Fretin.

## Rendering the scene

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

```js
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

## Click on button with aframe-htmlmesh

If you use [aframe-htmlmesh](https://github.com/AdaRoseCannon/aframe-htmlmesh), be aware this syntax doesn't work in VR:

```js
<button
  onClick={() => {
    AFRAME.scenes[0].exitVR();
  }}
>
  Exit Immersive
</button>
```

This works:

```js
<button
  // @ts-ignore
  onClick="AFRAME.scenes[0].exitVR();"
>
  Exit Immersive
</button>
```

or

```js
<button
  // @ts-ignore
  onClick={`AFRAME.scenes[0].exitVR();`}
>
  Exit Immersive
</button>
```

but if you introduce a variable in the template literal it becomes a function and won't work.
I think SolidJS expect a PointerEvent and not a MouseEvent, something like that.
I tried to emulate `pointerdown` followed by `pointerup` but it didn't work.
I found the following fix to work, but I'm not sure if it's a proper fix to contribute.
You can replace [here](https://github.com/AdaRoseCannon/aframe-htmlmesh/blob/fcedc6d86dcafc122183d518984f45c972e7b154/src/HTMLMesh.js#L527)

```js
element.dispatchEvent(new MouseEvent(event, mouseEventInit));
```

by

```js
if (event === "click") {
  // For SolidJS UI to work when onClick is a function and not a string
  element.click();
} else {
  element.dispatchEvent(new MouseEvent(event, mouseEventInit));
}
```

and the first syntax will work properly in VR.
