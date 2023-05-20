# Menus
Can't escape 2d interfaces even in a 3d world.

This page is a stub, but these are some of the best projects in this space:

# htmlmesh
project link: https://github.com/AdaRoseCannon/aframe-htmlmesh

Maintained. Confirmed working with A-Frame 1.4.0.

This component doesn't support:
- scrolling
- dynamically resizing the div ([draft PR](https://github.com/AdaRoseCannon/aframe-htmlmesh/pull/14))
- text input
- blur, focus, active, hover ([draft PR for hover on buttons](https://github.com/AdaRoseCannon/aframe-htmlmesh/pull/9), you need a css rule with :hover and .hover),
- svg
- css animations
- scaling the plane

It supports:
- images (but [without border/padding on them](https://github.com/mrdoob/three.js/pull/25925#issuecomment-1523743648), but you can use border/padding on the parent element)
- button
- radio
- range input,
- accent color, border-radius

It can be used to show normal html/css on desktop and rendered in 3d for VR only for example or rendered always in 3d even on desktop.
It is highly performant, only renders to canvas when necessary.
It's using a [modified copy](https://github.com/AdaRoseCannon/aframe-htmlmesh/blob/main/src/HTMLMesh.js) of the [HTMLMesh.js file that is also in the three.js repository](https://github.com/mrdoob/three.js/blob/dev/examples/jsm/interactive/HTMLMesh.js), so it will be probably maintained long term.
HTMLMesh is using the canvas API to draw the elements.

# htmlembed
project link: https://github.com/supereggbert/aframe-htmlembed-component

Not Maintained. Confirmed working with A-Frame 1.2.0. It works with A-Frame 1.3.0 and 1.4.0 if you replace `THREE.CanvasTexture` by `THREE.Texture` in `src/aframe-htmlembed-component.js`.

The htmlembed component is serializing the html to a string, create a svg with a foreignObject containing the serialized html, convert the svg to an image then draw the image to the canvas.

# three-mesh-ui
project link: https://github.com/felixmariotto/Three-Mesh-UI

There is no aframe component but you can create your component that use this library.

[Project Flowerbed](https://developer.oculus.com/blog/project-flowerbed-a-webxr-case-study/) used this library to create the UI. The UI is written in json, see one of the files in the [GitHub repository](https://github.com/meta-quest/ProjectFlowerbed/tree/main/content/ui) and the [UIPanelComponent](https://github.com/meta-quest/ProjectFlowerbed/blob/main/src/js/components/UIPanelComponent.js) creates the three-mesh-ui objects from the json.

# websurfaces
project link: https://github.com/ryota-mitarai/aframe-websurfaces

Render an iframe with CSS3DRenderer. This doesn't work in VR.

# Web2VR
project link: https://github.com/kikoano/web2vr

Confirmed working with A-Frame 1.3.0.

Note: this one is a bit non-standard in implementation, and has some quirks, but is very powerful and probably ideal for creating visually complex custom menus. Here's a glitch showing an attempt at using the project:
https://web2vr-demo.glitch.me/

Here's an issue where the non-standard interface is discussed, and how working around that is proposed: https://github.com/kikoano/web2vr/issues/18

# A-Frame Gui
project link: https://github.com/c-frame/aframe-gui

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
