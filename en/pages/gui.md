# Menus
Can't escape 2d interfaces even in a 3d world.

This page is a stub, but these are some of the best projects in this space:

# htmlmesh
project link: https://github.com/AdaRoseCannon/aframe-htmlmesh

This component doesn't support scrolling, dynamically resizing the div, text input, blur, focus, active, hover, image, svg, css animations, scaling the plane. It supports button, radio with accent color, input range, border-radius...
It can be used to show normal html/css on desktop and rendered in 3d for VR only for example or rendered always in 3d even on desktop.
It is highly performant, only renders to canvas when necessary.
It's using a modified copy of the HTMLMesh.js file that is also in the three.js repository, so it will be probably maintained long term.
HTMLMesh is using the canvas api to draw the elements.

This component has less features than htmlembed currently.
PR are pending to be merged to have hover on button element (you need a css rule with :hover and .hover), another PR for text wrapping, another one soon to be able to scale the plane.

# htmlembed
project link: https://github.com/supereggbert/aframe-htmlembed-component

The htmlembed component is serializing the html to a string, create a svg with a foreignObject containing the serialized html, convert the svg to an image then draw the image to the canvas.

# websurfaces
project link: https://github.com/ryota-mitarai/aframe-websurfaces

Render an iframe with CSS3DRenderer. This doesn't work in VR.

# Web2VR
project link: https://github.com/kikoano/web2vr

(confirmed working in A-Frame 1.3.0)

Note: this one is a bit non-standard in implementation, and has some quirks, but is very powerful and probably ideal for creating visually complex custom menus. Here's a glitch showing an attempt at using the project:
https://web2vr-demo.glitch.me/

Here's an issue where the non-standard interface is discussed, and how working around that is proposed: https://github.com/kikoano/web2vr/issues/18

# A-Frame Gui
project link: https://github.com/rdub80/aframe-gui

(Not currently updated for 1.3.0)

This project is probably the most 'A-Frame' native style option, and is a great starting point. It does need some bug fixes, but the code is fairly easy to work with and pull requests should be straightforward. Features powerful font options with troika-text, as well as beveled edges, natively.

# A-Frame Material Collection
project link: https://github.com/the-expanse/aframe-material-collection

Uses material design, and "yoga rendering engine" (see: https://yogalayout.com/). It's a bit old, and documentation is lacking, but still in use and recommended by arpu @ vrland.io. He maintains his own fork here, but it wasn't ready for public consumption at time of writing: https://github.com/vrlandio/aframe-material-collection

# Super-Keyboard
project link: https://github.com/supermedium/aframe-super-keyboard

A classic from supermedium. Allows deep customization, pretty advanced capabilities.

There is also a keyboard demo built-in to a-frame gui, above, but it wasn't component-ized, yet.

# aframe-vartiste-toolkit
project link: https://vartiste.xyz/docs.html

The aframe-vartiste-toolkit is a collection of components developed while creating [VARTISTE](https://vartiste.xyz). There's *a lot* of stuff in there, including many UI components. It can be useful for quickly putting together an icon-button and laser-pointer style interface. However, it's not always easy to pull in only parts of the toolkit.