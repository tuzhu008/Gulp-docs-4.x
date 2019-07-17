<!-- front-matter
id: creating-tasks
title: Creating Tasks
hide_title: true
sidebar_label: Creating Tasks
-->

# 创建任务

gulp 的每个任务都是一个异步的 JavaScript 函数，这个函数接收错误回调作为其参数，或者返回一个 stream、promise、event emitter、child process、或者 observable ([稍后会详细介绍][async-completion-docs])。
由于一些平台的限制，不支持同步任务，尽管有一个相当不错的[选择](using-async-await-docs)

## 导出

任务可以是 **public** 或 **private** 的。

* 从 gulpfile 导出的称为**公有任务**，可以使用 `gulp` 命令运行他们。
* 只用于在内部使用的称为**私有任务**，通常作为 `series()` 或 `parallel()`组合的一部分使用。


私有任务的外观和行为与任何其他任务一样，但是终端用户不能独立地执行它。要公开注册一个任务，请从 gulpfile 中导出它。

```js
const { series } = require('gulp');

// `clean` 没有被导出，因此可以将其视为私有任务。
// 可以在 `series()` 组合中使用。
function clean(cb) {
  // body omitted
  cb();
}

// `build` 函数被导出，因此他是一个公有任务，可以使用 `gulp` 运行
// 也可以在 `series()` 组合中使用
function build(cb) {
  // body omitted
  cb();
}

exports.build = build;
exports.default = series(clean, build);
```

![ALT TEXT MISSING][img-gulp-tasks-command]

<small>在过去，`task()` 用于将函数注册为任务。虽然该 API 仍然可用，但导出应该是主要的注册机制，除非在导出不起作用的边缘情况下。</small>

## 组合任务

Gulp 提供了两种强大的组合方法，`series()` 和 `parallel()`，允许将单个任务组合成更大的操作。这两种方法都接受任意数量的任务函数或组合操作。`series()` 和 `parallel()` 可以嵌套在它们内部，也可以彼此嵌套到任意深度。

要按顺序执行任务，请使用 `series()` 方法。

```js
const { series } = require('gulp');

function transpile(cb) {
  // body omitted
  cb();
}

function bundle(cb) {
  // body omitted
  cb();
}

exports.build = series(transpile, bundle);
```

要使任务以最大的并发性运行，请将它们与 `parallel()` 方法结合使用。

```js
const { parallel } = require('gulp');

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.build = parallel(javascript, css);
```

当调用 `series()` 或 `parallel()` 时，任务立即组成。这允许有条件地改变组合，而不是在单个任务中区分行为。

```js
const { series } = require('gulp');

function minify(cb) {
  // body omitted
  cb();
}


function transpile(cb) {
  // body omitted
  cb();
}

function livereload(cb) {
  // body omitted
  cb();
}

if (process.env.NODE_ENV === 'production') {
  exports.build = series(transpile, minify);
} else {
  exports.build = series(transpile, livereload);
}
```

`series()` 和 `parallel()` 可以嵌套到任意深度。

```js
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function cssTranspile(cb) {
  // body omitted
  cb();
}

function cssMinify(cb) {
  // body omitted
  cb();
}

function jsTranspile(cb) {
  // body omitted
  cb();
}

function jsBundle(cb) {
  // body omitted
  cb();
}

function jsMinify(cb) {
  // body omitted
  cb();
}

function publish(cb) {
  // body omitted
  cb();
}

exports.build = series(
  clean,
  parallel(
    cssTranspile,
    series(jsTranspile, jsBundle)
  ),
  parallel(cssMinify, jsMinify),
  publish
);
```

当运行组合操作时，每次引用的每个任务时都会被执行。例如，在两个不同任务之前引用的 `clean` 任务将运行两次，这不是期望的结果。因此，应该在最终的组合中指定 `clean`。

如果你有这样的代码:

```js
// 这是不正确的
const { series, parallel } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};

const css = series(clean, function(cb) {
  // body omitted
  cb();
});

const javascript = series(clean, function(cb) {
  // body omitted
  cb();
});

exports.build = parallel(css, javascript);
```

修改为：

```js
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

exports.build = series(clean, parallel(css, javascript));
```

[async-completion-docs]: ../getting-started/4-async-completion.md
[using-async-await-docs]: ../getting-started/4-async-completion.md#using-async-await
[img-gulp-tasks-command]: https://gulpjs.com/img/docs-gulp-tasks-command.png
[async-once]: https://github.com/gulpjs/async-once
