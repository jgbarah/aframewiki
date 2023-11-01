# FAQ

## WARNING: Multiple instances of Three.js being imported.
This can happen when you're importing some three addons that is not included in the aframe build.

If you don't use any build tool, what you can do is grab the addon file from the exact super-three version that is used by the aframe version you use, for example aframe 1.4.2 it's [https://github.com/supermedium/three.js/tree/super-147-1](https://github.com/supermedium/three.js/tree/super-147-1) 
then modify the import by the THREE global, like we did for the orbit-controls component [https://github.com/supermedium/superframe/blob/master/components/orbit-controls/lib/OrbitControls.js#L1-L5](https://github.com/supermedium/superframe/blob/master/components/orbit-controls/lib/OrbitControls.js#L1-L5)

Or if you use webpack and added the `three` dependency with npm (you should pin the exact version in `package.json` like `"three": "0.147.0"` if you use aframe 1.4.2), you can define an alias and external, relevant part here [https://github.com/c-frame/aframe-cursor-teleport/blob/master/webpack.config.js#L14-L17](https://github.com/c-frame/aframe-cursor-teleport/blob/master/webpack.config.js#L14-L17) and here [https://github.com/c-frame/aframe-cursor-teleport/blob/master/webpack.config.js#L31-L35](https://github.com/c-frame/aframe-cursor-teleport/blob/master/webpack.config.js#L31-L35)