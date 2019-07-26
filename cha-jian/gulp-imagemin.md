## 简介

Gulp 插件，用于对 PNG、JPEG、GIF 和 SVG 图片进行压缩，内部使用 [`imagemin`](https://github.com/imagemin/imagemin)。

[github 地址](https://github.com/sindresorhus/gulp-imagemin)

## 安装

```
npm install --save-dev gulp-imagemin
```

## 用法

### 基本用法

```js
const gulp = require('gulp');
const imagemin = require('gulp-imagemin');

function defaultTask() {
  return gulp.src('src/images/*')
    .pipe(imagemin())
    .pipe(gulp.dest('dist/images'))
}
```

### 自定义插件选项

```js
// …
.pipe(imagemin([
  imagemin.gifsicle({interlaced: true}),
  imagemin.jpegtran({progressive: true}),
  imagemin.optipng({optimizationLevel: 5}),
  imagemin.svgo({
    plugins: [
      {removeViewBox: true},
      {cleanupIDs: false}
    ]
  })
]))
// …
```

注意，您可能会遇到一种更古老的隐式语法。在版本 &lt; 3 中，是这样写的:

```js
// …
.pipe(imagemin({
  interlaced: true,
  progressive: true,
  optimizationLevel: 5,
  svgoPlugins: [
    {
      removeViewBox: true
    }
  ]
}))
// …
```

### 自定义插件选项和自定义 `gulp-imagemin` 选项

```js
// …
.pipe(imagemin([
  imagemin.svgo({
    plugins: [
      {
        removeViewBox: true
      }
    ]
  })
], {
  verbose: true
}))
// …
```

## API

使用捆绑的**无损压缩器：**

* [gifsicle](https://github.com/imagemin/imagemin-gifsicle) — Compress GIF images
* [jpegtran](https://github.com/imagemin/imagemin-jpegtran) — Compress JPEG images
* [optipng](https://github.com/imagemin/imagemin-optipng) — Compress PNG images
* [svgo](https://github.com/imagemin/imagemin-svgo) — Compress SVG images

这些是为了方便而捆绑的，大多数用户不需要其他任何东西。

### imagemin\(plugins?, options?\)

不支持的文件将被忽略。

#### plugins

类型：`Array`

默认值：`[imagemin.gifsicle(), imagemin.jpegtran(), imagemin.optipng(), imagemin.svgo()]`

要使用的 [插件](https://www.npmjs.com/browse/keyword/imageminplugin)。这将覆盖默认插件。注意，默认插件带有良好的默认值，在大多数情况下应该足够了。有关受支持的选项，请参阅各个插件。

#### options

类型：`object`

##### verbose

类型: `boolean`

默认值: `false`

启用此功能将记录每一个传递给 `gulp-imagemin`  的图像的信息:

```
gulp-imagemin: ✔ image1.png (already optimized)
gulp-imagemin: ✔ image2.png (saved 91 B - 0.4%)
```



