## 简介

Gulp 的 [Sass](https://www.sass.hk/) 插件，用于解析 `.sass` 文件。

[github 地址](https://github.com/dlmanning/gulp-sass)

## 安装

```
npm install node-sass gulp-sass --save-dev
```

## 用法

```js
'use strict';

var gulp = require('gulp');
var sass = require('gulp-sass');

sass.compiler = require('node-sass');

function sassTask() {
  return gulp.src('./sass/**/*.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest('./css'));
};

function watchSass() {
  gulp.watch('./sass/**/*.scss', ['sass']);
};
```

## 选项

全部可用的选项请参见 [LESS 官网 ](http://lesscss.org/#using-less-configuration)。以下是有效的选项列表：

* `paths`：用于 `@import` 指令的路径数组

* `plugins`：less 插件的数组\(详细信息\)

`filename` 选项是不必要的，它由本插件自动处理。`compress` 选项不受支持——如果您试图缩小您的 css，请使用 css 压缩器。不支持 `sourceMap` 选项——如果您试图生成 sourceMap，请使用 [gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md)。

## 使用插件

Less 支持插件，可以添加额外的功能。下面是一个如何在 `gulp-less`  使用插件的例子。

```js
var LessAutoprefix = require('less-plugin-autoprefix');
var autoprefix = new LessAutoprefix({ browsers: ['last 2 versions'] });

function lessTask() {
  var LessAutoprefix = require('less-plugin-autoprefix');
  var autoprefix = new LessAutoprefix({ browsers: ['last 2 versions'] });

  return gulp.src('./less/**/*.less')
  .pipe(less({
    plugins: [autoprefix]
  }))
  .pipe(gulp.dest('./public/css'));
}
```

更多关于 LESS 插件的信息可以在 [http://lesscss.org/usage/\#plugins](http://lesscss.org/usage/#plugins) 找到。

## Source Maps

`gulp-less` 可以与 [gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md) 一起使用，用以为文件生成源映射。您将需要在运行 gulp-less 编译器之前初始化 [gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md)，然后编写源代码映射，如下所示:

```js
var sourcemaps = require('gulp-sourcemaps');

function lessTask() {
  return gulp.src('./less/**/*.less')
    .pipe(sourcemaps.init())
    .pipe(less())
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('./public/css'));
}
```

默认情况下，[gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md) 将源映射内联到已编译的 CSS 文件中。要将它们写入单独的文件，请在 `sourcemaps.write()` 函数中指定一个相对文件路径，如下所示:

```js
var sourcemaps = require('gulp-sourcemaps');

function lessTask() {
  return gulp.src('./less/**/*.less')
    .pipe(sourcemaps.init())
    .pipe(less())
    .pipe(sourcemaps.write('./maps'))
    .pipe(gulp.dest('./public/css'));
}
```

## 错误处理

默认情况下，当发生错误时，gulp 任务将失败，所有流将停止。要更改此行为，请查看[此处](https://github.com/gulpjs/gulp/blob/master/docs/recipes/combining-streams-to-handle-errors.md)的错误处理文档。

