# Environment
Setting the scene around you is a feature of most projects. Here's some thoughts on doing so.
# Quick Start
Here's two packages that auto-generate environments for you, with tweakable params:
## aframe-environment-component
Source: https://github.com/supermedium/aframe-environment-component
This is a classic from the early days that gives a wide range of vibes with a lot of tweakable params. Most quick demos you see rely on this package. High performance, and extremely easy to integrate. Two lines of HTML, and you have a stage. Developed by core contributors at supermedium.
## aframe-enviropacks
Source: https://www.npmjs.com/package/aframe-enviropacks
A more recent contribution featuring more attrative textures. Haven't worked with it yet, but a lot less 'basic' looking than the OG.
# Sky
Most projects will want some kind of sky.
## Full Sky System
### A-Starry-Sky
[source](https://github.com/Dante83/A-Starry-Sky)
(see there for examples)
A GPU intense, but incredibly accurate and beautiful sky system.
### a-super-sky
[source](https://github.com/kylebakerio/a-super-sky)
[example](https://glitch.com/edit/#!/a-super-sky-demo?path=index.html%3A1%3A0)
Based on the sun-sky shader, as well as code pulled and integrated from the aframe-environment component, this repo gives an out-of-the-box ***animated*** sky with sun, moon, stars, and a mixture of shadow-casting sun and ambient lighting with color and intensity varying accordingly.
### supermedium's sun-sky component/shader
[source](https://github.com/supermedium/superframe/tree/master/components/sun-sky/)
[variation](https://github.com/aframevr/aframe/blob/b164623dfa0d2548158f4b7da06157497cd4ea29/examples/test/shaders/shaders/sky.js) in the aframe repository's examples that may be an updated version. This version seems to be identical to that used in the aframe-environment-component as well.
[example](https://supermedium.com/superframe/components/sun-sky/)
Classic project with beautiful visuals and high performance. Canonical examples are using older versions of A-Frame, but there are no real compatibility issues with newer versions. The animated example is currently incompatible with modern a-frame, which led to the development of a-super-sky; note that this sky shader lives on in the aframe-environment-component
## just sky
### simple-sun-sky
[source](https://github.com/DougReeder/aframe-simple-sun-sky)
[example](https://dougreeder.github.io/aframe-simple-sun-sky/example.html)
Lightweight, simple gradient + sun.
## just stars
### aframe-star-system-component
[source](https://github.com/handeyeco/aframe-star-system-component)
Works for pre A-Frame 1.2.0. To get the same effect > 1.2.0, see the example of stars used in `a-super-sky` or `aframe-environment-component`.
# Buildings
### aframe-shader-buildings
[source](https://github.com/DougReeder/aframe-shader-buildings)
Add a city's worth full of low-poly buildings, cheap!
Now supports textures, and updated for compatibility with A-Frame 1.4.0.
