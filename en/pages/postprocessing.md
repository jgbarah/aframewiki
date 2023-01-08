# Postprocessing

As of version 1.4.1 (January 2023), A-Frame doesn't natively support three.js postprocessing, but there are ways to make it work.
Here's an [example](https://glitch.com/edit/#!/anaglyph-postprocessing?path=index.html) showing the anaglyph postprocessing effect that Diarmid did a little while ago.
Similar techniques will probably work for other postprocessing effects.

See also [this discussion started in June 2018](https://github.com/aframevr/aframe/pull/3645) about introducing a postprocessing API in A-Frame.

Hubs integrated (October 2022) in their [scene renderer](https://github.com/mozilla/hubs/pull/5742) the [postprocessing library](https://www.npmjs.com/package/postprocessing)
but they don't use the A-Frame renderer anymore, so finding a good API for A-Frame from the discussion above is still needed.
Some tweets about the bloom effect in Hubs:

- https://twitter.com/MozillaHubs/status/1588516422568869894
- https://twitter.com/MozillaHubs/status/1587429257453735937
- https://twitter.com/MozillaHubs/status/1572571349796765697
