<!-- front-matter
id: parallel
title: parallel()
hide_title: true
sidebar_label: parallel()
-->

# parallel()

将任务功能和/或组合操作组合成同时执行的较大操作。对于使用 `series()` 和 `parallel()` 组合操作的嵌套深度没有强制限制。

## 用法

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

## 签名

```js
parallel(...tasks)
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:------:|-------|
| tasks<br>**(required)** | function<br>string | 任意数量的任务函数都可以作为单独的参数传递。如果您以前注册过任务，可以使用字符串，但不建议这样做。 |

### 返回值

返回一个组合操作，它将注册为任务或嵌套在其他 `series` 和/或 `parallel` 组合中。


当执行组合操作时，所有任务将以最大并发性运行。如果一个任务发生错误，其他任务可能不确定地完成，也可能不完成。

### Errors

当没有传递任何任务时，抛出一个错误，并提示 "One or more tasks should be combined using series or parallel"。（一个或多个应该使用 series 或 parallel 组合的任务”。）

当传递无效的任务或未注册的任务时，将抛出一个错误，显示 "Task never defined"（任务从未定义）。

## Forward references

向前引用是指使用尚未注册的字符串引用组合任务。在旧版本中，这是一种常见的实践，但是为了实现更快的任务运行时间和促进使用命名函数，删除了该特性。

在较新的版本中，如果尝试使用向前引用，将会得到一个错误，消息为 “Task never defined”。当您尝试为您的任务注册*和*按字符串组合任务使用 `exports` 时，可能会遇到这种情况。在这种情况下，使用命名函数而不是字符串引用。

在迁移期间，您可能需要使用 [forward reference registry][undertaker-forward-reference-external]。这将为每个任务引用添加一个额外的闭包，并显著降低构建速度。**不要太长时间依赖这个修复程序**。

## 避免重复任务

当运行组合操作时，每个任务将在每次提供时执行。

在两个不同的组合中引用的 `clean` 任务将运行两次，将导致不期望的结果。相反，在最终组合中指定的 `clean` 任务。


如果有这样的代码:

```js
// This is INCORRECT
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

Migrate to this:
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

[undertaker-forward-reference-external]: https://github.com/gulpjs/undertaker-forward-reference
