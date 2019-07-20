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
可选值：`1`\(依靠蛮力\) to`11`\(最快\)

speed `10 `的质量要比默认速度低 5%，但速度大约是默认速度的 8 倍。speed `11` 禁止混色，并会降低压缩级别。

##### strip

类型：`boolean`  
默认值：`false`

删除可选的元数据。

##### quality

类型：`Array<min: number, max: number>`  
可选值：`Array<0...1, 0...1>`  
例如：`[0.3, 0.5]`

指示 pngquant 使用满足或超过最大质量所需的最少数量的颜色。如果转换结果的质量低于最低质量的图像将不会被保存。

Min 和 max 是 0\(最差\)到 1\(完美\)范围内的数字，类似于 JPEG。

##### dithering

类型：`number | boolean`  
默认值：`1`\(full\)  
可选值：`0...1`

使用 0 \(none\) 和 1 \(full\)之间的小数设置混色级别。

设置为 `false` 来禁止混色

##### posterize

类型：`number`

截断最不重要的颜色位\(每个通道\)的数目。当图像将在低深度显示器\(如 16 位RGB\)上输出时，请使用此选项。pngquant 将使几乎不透明的像素完全不透明，并将减少半透明颜色的数量。

##### verbose

类型：`boolean`  
默认值：`false`

打印详细的状态消息。

#### input

类型：`Buffer | Stream`

要优化的缓冲区或流。

