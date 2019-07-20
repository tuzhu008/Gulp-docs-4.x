## 简介

Gulp 插件，用于压缩 png 图片。

[github 地址](https://github.com/dlmanning/gulp-sass)

## 安装

```
npm install --save-dev imagemin-pngquant
```

## 用法

```js
const imagemin = require('imagemin');
const imageminPngquant = require('imagemin-pngquant');

(async () => {
  await imagemin(['images/*.png'], 'build/images', {
    plugins: [
      imageminPngquant()
    ]
  });

  console.log('Images optimized');
})();
```

## API

### imageminPngquant\(options?\)\(input\)

返回 `Promise<Buffer>`.

#### options

类型：`object`

##### speed

类型：`number`  
默认值：`4`  
可选值：`1`\(brute-force\) to`11`\(fastest\)

Speed`10`has 5% lower quality, but is about 8 times faster than the default. Speed`11`disables dithering and lowers compression level.

##### strip

类型：`boolean`  
默认值：`false`

Remove optional metadata.

##### quality

类型：`Array<min: number, max: number>`  
可选值：`Array<0...1, 0...1>`  
例如：`[0.3, 0.5]`

Instructs pngquant to use the least amount of colors required to meet or exceed the max quality. If conversion results in quality below the min quality the image won't be saved.

Min and max are numbers in range 0 \(worst\) to 1 \(perfect\), similar to JPEG.

##### dithering

类型：`number | boolean`  
默认值：`1`\(full\)  
可选值：`0...1`

Set the dithering level using a fractional number between 0 \(none\) and 1 \(full\).

Pass in`false`to disable dithering.

##### posterize

类型：`number`

Truncate number of least significant bits of color \(per channel\). Use this when image will be output on low-depth displays \(e.g. 16-bit RGB\). pngquant will make almost-opaque pixels fully opaque and will reduce amount of semi-transparent colors.

##### verbose

类型：`boolean`  
默认值：`false`

Print verbose status messages.

#### input

类型：`Buffer | Stream`

Buffer or stream to optimize.



