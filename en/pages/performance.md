# Performance

Some very rough notes. Also see [GLTF/ GLB](gltf.md)

## Native vs. WebGL

Here's the truth: Using WebGL for your rendering IS slower than doing the same thing with native code. But it's only slower in very specific ways, and if you know what they are you can work on avoiding them.

When dealing with performance problems in 3D rendering, we can broadly place our bottlenecks into two categories: Fill Rate limited and Draw Call limited.

Fill Rate problems happen when your GPU simply can't keep up with the pixels you are asking it to draw. This typically happens because you've got a shader that's really complex or are drawing tons of polygons, especially if you've got things like lots of overlapping partially transparent geometry. When you're Fill Rate limited you generally want to try some combination of making your shaders less expensive, reducing how much you're drawing and how many lights you're using, and shrinking the resolution that you're rendering at. Basically anything that cuts down on how intensive it is to draw a pixel and how many of those pixels you're drawing.

The thing to realize about being Fill Rate limited, though, is that whether you're using WebGL or some native engine, your GPU is still running things at the same speed. The web may introduce a small amount of overhead for compositing the page, but nothing about your GPU's processing power magically gets faster when using native code. Take [ShaderToy](https://www.shadertoy.com), for example: NOTHING on that site is Draw Call limited. None of the shaders wouldn't any better if you drew the same effect using the most beautifully optimized native code in the world.

Where the web has a harder time relative to native code is when you're Draw Call limited, and sadly it's really easy to fall into that bucket. When you're Draw Call limited it's because your code can't feed commands to the GPU fast enough, and so your GPU is just sitting around waiting for you to tell it what to draw. This can be because your code is taking too long to make the draw calls (for example: you're waiting for physics code to finish before you start drawing), the calls you're making are really expensive (ie: Uploading large textures or vertex buffers every frame), or because you're making a lot of not-terribly-expensive calls that quickly add up. In my experience that last scenario is often the most likely.

Native code can be better at this part of things for a few reasons. There's newer graphics APIs available to native code (like Metal, Vulkan, and D3D12) that can tell the GPU what to draw more efficiently. This will be mitigated quite a bit when [WebGPU](https://gpuweb.github.io/gpuweb/) ships in the near-ish future. In the meantime we've only got WebGL to work with, which is based on the pretty old OpenGL standard and is unfortunately verbose for a lot of common operations. Using features from WebGL 2.0 when possible is the best way to close the gap until then. (Libraries like Babylon.js will do this for you automatically.) Native code also has less overhead than JavaScript, so every function call in JavaScript tends to cost a little bit more, and native code tends to have better ways to parallelize work.

Even so, it's easy to become Draw Call limited in native code as well, and the biggest difference is that the popular engines like Unity and Unreal have more robust tools to help you fix it. There's no magic bullets to be had here, though, and most of the best performing native apps need to go through the same types of asset optimization that will benefit your web app.

## Measuring Performance

On desktop use [A-Frame stats component](https://aframe.io/docs/master/components/stats.html)

In VR, use [vr-super-stats](https://github.com/kylebakerio/vr-super-stats)

Use chrome://inspect to attach the Chrome Debugger to your VR device / browser.

Use the Performance tab to record a sample from your app. &nbsp;You can then analyze the sample, and review which function calls are taking up the time.

vr-super-stats also includes a profiling functionality

## Oculus Quest Performance

There's a ton of really detailed and useful advice from Oculus for Developers [here](https://developer.oculus.com/documentation/web/webxr-perf/)

While it's mostly targeted at VR experiences on Oculus Quest, a lot of the advice is more broadly applicable, so could be useful even if you are targetting other platforms.

## Improving Performance

Lots of excellent advice in the [A-Frame Best Practices](https://aframe.io/docs/master/introduction/best-practices.html#performance)

Also lots of very good advice from Oculus for Developers [here](https://developer.oculus.com/documentation/web/webxr-perf/), here is [another page](https://developer.oculus.com/documentation/web/webxr-perf-bp/) where they give a list of best practices to consider.

A great tutorial on how to adjust GLTF models & textures to dramatically improve performance [here](https://toji.github.io/webxr-scene-optimization/).

## Physics Performance

An example of some performance data from aframe-physics-system Ammo.js, gained by measuring the length of each physics tick()

[https://diarmidmackenzie.github.io/christmas-scene/research/perf/physics-performance.html](https://diarmidmackenzie.github.io/christmas-scene/research/perf/physics-performance.html)

Nvidia PhysX can deliver better performance than Ammo.js (approx 3x better), but overall support for PhysX in A-Frame is less matrue than Ammo.js

There is a basic implementation of PhysX for A-Frame as part of [Vartiste Toolkit](https://www.npmjs.com/package/aframe-vartiste-toolkit). (but it has some performance issues).

These links show benchmark tests comparing Ammo.js and PhysX (alightly modified implementation of PhysX, fixing some of the perf issues).

- [Ammo.js](https://diarmidmackenzie.github.io/christmas-scene/research/physics-benchmarks/benchmark1-ammo.html)
- [PhysX](https://diarmidmackenzie.github.io/christmas-scene/research/physics-benchmarks/benchmark1-physx.html)

## Instancing

[aframe-instanced-mesh](https://www.npmjs.com/package/aframe-instanced-mesh) provides pretty comprehensive instancing support, in a way that allows each instance to be modelled by a separate DOM entity.

## Merging Geometries

For static non-repeating geometries, merging is probably a better solution than instancing.<br><br>I'm not aware of an A-Frame component that simplifies this, but THREE.js [BufferGeometryUtils](https://threejs.org/docs/#examples/en/utils/BufferGeometryUtils) provides `mergeBufferGeometries` which is fairly easy to use. (note: there was an old one in the supermedium repo, but it probably fell out of date and wasn't updated as three geometry was updated. may be a useful starting point though.)

See [this module][https://github.com/diarmidmackenzie/christmas-scene/blob/main/research/composite-objects/custom-geometries.js] for a range of examples of how this can be used (both with single & multiple materials).

## A-frame vs. Babylon.js

Some notes in response to a query whether A-Frame performance was better or worse than Babylon.js, and whether A-Frame made use of worker threads to boost performance.

- Profiling code shows that THREE.js is doing the vast majority of processing, so the comparison to make is really Babylon vs. THREE.js, rather than Babylon vs. A-Frame. And the answer there seems to be that they are about the same... [https://forum.babylonjs.com/t/does-babylon-js-or-three-js-perform-better-with-more-meshes/7505/3](https://forum.babylonjs.com/t/does-babylon-js-or-three-js-perform-better-with-more-meshes/7505/3)
- Key perf bottlenecks that I have hit in my projects have been (a) draw calls and (b) physics.
- For draw calls, instancing and merging geometries are key. Get those right, and you can do a lot without hitting the draw calls bottleneck.
- For physics, I believe the most performant solution is to use PhysX, which offloads physics calculations to the GPU. Unfortunately most of my experience so far has been using Ammo.js on the CPU, where performance constraints start to get felt pretty quickly.
- A-Frame Physics System is the only example I have seen of an A-Frame component using worker threads. In general, the tradeoff seems to be ease of use vs. performance, with easiest to use and worst performance being cannon, middle of the road on both being ammo, and the best performance but most incomplete and difficult implementation being PhysX.
