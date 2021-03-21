# R2R Logo Generator

This generator typesets arbitrary words in the style of the logo of the DiVOC easter event 2021: [Reboot to Respawn](https://di.c3voc.de/r2r:start). [You can use the generator online.](https://malteschmitz.github.io/r2r/) It is written purely in JavaScript without libraries using the [built-in SVG support](https://developer.mozilla.org/en-US/docs/Web/API/SVGElement) of modern browsers.

The code uses the [Itim font](https://fonts.google.com/specimen/Itim) which is available on Google fonts. Unfortunately SVG doesn't have much support for manipulating texts. You can include text in an SVG, but you cannot manipulate the text as vectors. To overcome this limitation the letters A to Z and 0 to 9 are converted into [SVG paths](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths) using [Inkscape](https://inkscape.org). These paths are store in the the `font` array.

The generation is performed in the following steps:

1. The individual letters of the text are typeset next to each other as path elements.
1. For every letter 100 positions of its outline are extracted using [getPointAtLength](https://developer.mozilla.org/en-US/docs/Web/API/SVGGeometryElement/getPointAtLength). Some letters like A, O or Q have inner regions which should be excluded from the outline. This is achieved by cloning the paths and updating their `d` attribute to contain only the [path commands](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#path_commands) until the first `z`. (`z` is the command which closes the current path.)
1. To attach the icons to the bottom, top or right of the letters, we consider the points with a maximal or minimal X or Y coordinate with a certain tolerance. From all the points inside the tolerance one is randomly chosen and taken as reference position for the icon. The icons are added as path objects and moved and scaled using the functions [translate](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/transform#translate) and [scale](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/transform#scale) in its [transform](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/transform) attribute.
1. For the resistors connecting the letters we consider all the points with a Y coordinate close to the center of the letter (with a certain tolerance). From those points we consider all the right points (again with a certain tolerance) and chose one of those randomly. Now we consider all the points of the next letter whose Y coordinate is the same as our chosen point's (with a certain tolerance). From those we consider all the left points (again with a certain tolerance) and chose one of those randomly. The resistor icon is now drawn between these two points by setting its transform attribute to `translate(${a.x},${a.y}) scale(${distance(a,b) / 20}) rotate(${angle(a, b)})`.

This process is visualized and explained (in German) in this video:

<iframe src="https://archive.org/embed/2021-02-24-5min-einen-logo-generator-fuer-divoc-reboot-to-respawn" width="640" height="480" frameborder="0" webkitallowfullscreen="true" mozallowfullscreen="true" allowfullscreen></iframe>

[The slides can be found here in a separate repository.](https://github.com/malteschmitz/r2r-logo-talk)
