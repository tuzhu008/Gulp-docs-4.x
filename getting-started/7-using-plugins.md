<!-- front-matter
id: using-plugins
title: Using Plugins
hide_title: true
sidebar_label: Using Plugins
-->

# 使用插件

Gulp 插件是 [Node Transform Streams][through2-docs]，它封装了在管道中转换文件的常见行为——通常使用 `.pipe()` 方法放在 `src()` 和`dest()` 之间。它们可以更改通过流传输的每个文件的文件名、元数据或内容。

来自 npm 的插件——使用 “gulpplugin” 和 “gulpfriendly” 关键字——可以在[插件搜索页面][gulp-plugin-site]上浏览和搜索。

每个插件应该只做少量的工作，所以您可以像构建块一样连接它们。您可能需要将它们组合起来才能得到所需的结果。

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');
  const rename = require('gulp-rename');

exports.default = function() {
  return src('src/*.js')
    // The gulp-uglify plugin won't update the filename
    .pipe(uglify())
    // So use gulp-rename to change the extension
    .pipe(rename({ extname: '.min.js' }))
    .pipe(dest('output/'));
}
```

## 是否需要插件吗?

并不是 gulp 中的所有东西都应该使用插件。这是一种快速入门的方法，但是许多操作都通过使用模块或库进行了改进。

```js
const { rollup } = require('rollup');

// Rollup's promise API works great in an `async` task
exports.default = async function() {
  const bundle = await rollup.rollup({
    input: 'src/index.js'
  });

  return bundle.write({
    file: 'output/bundle.js',
    format: 'iife'
  });
}
```

插件应该总是转换文件。为任何其他操作使用(非插件) Node 模块或库。

```js
const del = require('delete');

exports.default = function(cb) {
  // Use the `delete` module directly, instead of using gulp-rimraf
  del(['output/*.js'], cb);
}
```

## 条件式插件

由于插件操作不应该是文件类型敏感的，您可能需要一个类似 [gulp-if][gulp-if-package] 这样的插件来转换文件的子集。

```js
const { src, dest } = require('gulp');
const gulpif = require('gulp-if');
const uglify = require('gulp-uglify');

function isJavaScript(file) {
  // Check if file extension is '.js'
  return file.extname === '.js';
}

exports.default = function() {
  // Include JavaScript and CSS files in a single pipeline
  return src(['src/*.js', 'src/*.css'])
    // Only apply gulp-uglify plugin to JavaScript files
    .pipe(gulpif(isJavaScript, uglify()))
    .pipe(dest('output/'));
}
```

## 内联插件

内联插件是通过在 gulpfile 中编写所需的行为定义的一次性转换流。

创建内联插件有两种情况:
* 不用创建和维护你自己的插件。
* 不用 fork 一个插件来添加你想要的功能。

```js
const { src, dest } = require('gulp');
const uglify = require('uglify-js');
const through2 = require('through2');

exports.default = function() {
  return src('src/*.js')
    // Instead of using gulp-uglify, you can create an inline plugin
    .pipe(through2.obj(function(file, _, cb) {
      if (file.isBuffer()) {
        const code = uglify.minify(file.contents.toString())
        file.contents = Buffer.from(code)
      }
      cb(null, file);
    }))
    .pipe(dest('output/'));
}
```

[gulp-plugin-site]: https://gulpjs.com/plugins/
[through2-docs]: https://github.com/rvagg/through2
[gulp-if-package]: https://www.npmjs.com/package/gulp-if
