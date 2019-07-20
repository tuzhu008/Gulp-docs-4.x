## 简介

Gulp 的 [LESS](http://lesscss.org/) 插件，用于解析 `.less` 文件。

[github 地址](https://github.com/gulp-community/gulp-less)

## 安装

```
npm install --save-dev gulp-less
```

## 用法

```js
var less = require('gulp-less');
var path = require('path');

function lessTask() {
  return gulp.src('./less/**/*.less')
    .pipe(less({
      paths: [ path.join(__dirname, 'less', 'includes') ]
    }))
    .pipe(gulp.dest('./public/css'));
});
```

## 选项

全部可用的选项请参见 [LESS 官网 ](http://lesscss.org/#using-less-configuration)。以下是有效的选项列表：

* `paths`：用于 `@import` 指令的路径数组

* `plugins`：less 插件的数组\(详细信息\)

`filename` 选项是不必要的，它由本插件自动处理。`compress` 选项不受支持——如果您试图缩小您的 css，请使用 css 压缩器。不支持 `sourceMap` 选项——如果您试图生成 sourceMap，请使用 [gulp-sourcemaps](https://github.com/floridoo/gulp-sourcemaps)。

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



