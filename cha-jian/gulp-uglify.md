## 简介

基于 [UglifyJS](https://github.com/mishoo/UglifyJS2) 的 gulp 插件，用于对 JavaScript 进行缩小化，压缩、美化。

## 安装

```
npm install --save-dev gulp-uglify
```

## 用法

```js
var gulp = require('gulp');
var uglify = require('gulp-uglify');
var pipeline = require('readable-stream').pipeline;

function compressTask() {
    return pipeline(
        gulp.src('lib/*.js'),
        uglify(),
        gulp.dest('dist')
    );
}
```

## 选项

支持 UglifyJS API 的大多数优化选项，但也有几个例外:

* 不能设置 `sourceMap` 选项，因为它将根据您的 Gulp 配置自动配置。参见 [Gulp sourcemaps](https://github.com/gulp-sourcemaps/gulp-sourcemaps#usage) 的文档。

## 错误

如果无法优化特定文件，`gulp-uglify` 将发出一个 `error` 事件。GulpUglifyError 构造函数由这个插件导出，用于 `instanceof` 检查。它包含以下属性:

* `fileName`：正在优化的文件的完整文件路径。

* `cause`：原始的 UglifyJS 错误，如果可用的话。

大多数 UglifyJS 错误消息都有以下属性:

* `message`（or `msg`\)
* `filename`
* `line`

## 使用不同版本的 UglifyJS

默认情况下，`gulp-uglify` 使用作为其依赖安装的 `UglifyJS` ,但也可以使用不同版本的 `UglifyJS`：

```js
var uglifyjs = require('uglify-js'); // can be a git checkout
                                     // or another module (such as `uglify-es` for ES6 support)
var composer = require('gulp-uglify/composer');
var pump = require('pump');

var minify = composer(uglifyjs, console);

function compressTask(cb) {
  // the same options as described above
  var options = {};

  pump([
      gulp.src('lib/*.js'),
      minify(options),
      gulp.dest('dist')
    ],
    cb
  );
}
```



