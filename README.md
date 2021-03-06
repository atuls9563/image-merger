# imageMerger

> Easily compose images together without messing around with canvas

Canvas can be kind of a pain to work with sometimes, especially if you just need a canvas context to do something relatively simple like merge some images together. `imageMerger` abstracts away all the repetitive tasks into one simple function call.

Images can be overlaid on top of each other and repositioned. The function returns a Promise which resolves to a base64 data URI. Supports both the browser and Node.js.

This is an updated version of `merge-images` by Luke Childs with all libraries updated and multi operating system testing.  `xo` is removed as this linting caused many issues.

The major update from `merge-images` is , dynamic height of canvas based on input images actual sizes, it resize the images propotionally based on max width & height passed as option

## Install

```shell
npm install --save imageMerger
```

## Usage

With the following images:

`/body.png`|`/eyes.png`|`/mouth.png`
---|---|---
<img src="/src/sample-images/body.png" width="128">|<img src="/src/sample-images/eyes.png" width="128">|<img src="/src/sample-images/mouth.png" width="128">

You can do:

```js
import imageMerger from 'imageMerger';

imageMerger(['/body.png', '/eyes.png', '/mouth.png'])
  .then(b64 => document.querySelector('img').src = b64);
  // data:image/png;base64,iVBORw0KGgoAA...
```

And that would update the `img` element to show this image:

<img src="/src/sample-images/face.png" width="128">

### Positioning

Those source png images were already the right dimensions to be overlaid on top of each other. You can also supply an array of objects with x/y co-ords to manually position each image:

```js
imageMerger([
  { src: 'body.png', x: 0, y: 0 },
  { src: 'eyes.png', x: 32, y: 0 },
  { src: 'mouth.png', x: 16, y: 0 }
])
  .then(b64 => ...);
  // data:image/png;base64,iVBORw0KGgoAA...
```

Using the same source images as above would output this:

<img src="/src/sample-images/face-custom-positions.png" width="128">

### Resize

Each source image width and height can be adjusted.

```js
imageMerger([{ src: 'face.png', width: 128, height: 128 }]
```
You must be set `width` and `height` otherwise you can't see the source image.

### Opacity

The opacity can also be tweaked on each image.

```js
imageMerger([
  { src: 'body.png' },
  { src: 'eyes.png', opacity: 0.7 },
  { src: 'mouth.png', opacity: 0.3 }
])
  .then(b64 => ...);
  // data:image/png;base64,iVBORw0KGgoAA...
```

<img src="/src/sample-images/face-opacity.png" width="128">

### Dimensions

By default the new image dimensions will be set to the width of the widest source image and the height of the tallest source image. You can manually specify your own dimensions in the options object:

```js
imageMerger(['/body.png', '/eyes.png', '/mouth.png'], {
  width: 128,
  height: 128
})
  .then(b64 => ...);
  // data:image/png;base64,iVBORw0KGgoAA...
```

Which will look like this:

<img src="/src/sample-images/face-custom-dimension.png" width="64">

## Node.js Usage

Usage in Node.js is the same, however you'll need to also require [node-canvas](https://github.com/Automattic/node-canvas) and pass it in via the options object.

```js
import imageMerger from 'imageMerger';
import Canvas from 'canvas';

imageMerger(['./body.png', './eyes.png', './mouth.png'], {
  Canvas: Canvas
})
  .then(b64 => ...);
  // data:image/png;base64,iVBORw0KGgoAA...
```

One thing to note is that you need to provide a valid image source for the node-canvas `Image` rather than a DOM `Image`. Notice the above example uses a file path, not a relative URL like the other examples. Check the [node-canvas docs](https://github.com/Automattic/node-canvas) for more information on valid `Image` sources.

## API

### imageMerger(images, [options])

Returns a Promise which resolves to a base64 data URI

#### images

Type: `array`<br>
Default: `[]`

Array of valid image sources for `new Image()`.<br>
Alternatively an [array of objects](#positioning) with `x`/`y` co-ords and `src` property with a valid image source.

#### options

Type: `object`

##### options.format

Type: `string`<br>
Default: `'image/png'`

A DOMString indicating the image format.

##### options.quality

Type: `number`<br>
Default: `0.92`

A number between 0 and 1 indicating image quality if the requested format is image/jpeg or image/webp.

##### options.width

Type: `number`<br>
Default: `undefined`

The width in pixels the rendered image should be. Defaults to the width of the widest source image.

##### options.height

Type: `number`<br>
Default: `undefined`

The height in pixels the rendered image should be. Defaults to the height of the tallest source image.

##### options.Canvas

Type: `Canvas`<br>
Default: `undefined`

Canvas implementation to be used to allow usage outside of the browser. e.g Node.js with node-canvas.

## License

MIT
