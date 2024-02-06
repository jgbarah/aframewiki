# Animation

If you want to keyframe states / transitions, use the built-in aframe `animation` component as described in the [docs](https://aframe.io/docs/master/components/animation.html).
If you want to use animations baked into your gltf, use the [animation-mixer](https://github.com/c-frame/aframe-extras/tree/master/src/loaders)

To run mixamo animation from a separate glb file on your avatar model, see akbartus's [AFrame GLTF Animations in the Runtime](https://github.com/akbartus/AFrame-Runtime-GLTF-Animations) example.
If you need to retarget an animation from a skeleton to another, read this [comment](https://github.com/c-frame/aframe-extras/issues/423#issuecomment-1432883457)

If you're using fullbody avatars with networked-aframe, read [How to only sync one axis of an object’s rotation?](https://github.com/networked-aframe/networked-aframe/discussions/404) to be sure you're syncing only the y rotation of your avatar.

See this complete working example of using [realistic animated avatars in networked-aframe](https://github.com/networked-aframe/naf-valid-avatars).
