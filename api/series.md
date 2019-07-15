<!-- front-matter
id: series
title: series()
hide_title: true
sidebar_label: series()
-->

# series()

将任务函数和/或组合操作组合成更大的操作，这些操作将按顺序依次执行。对于使用 `series()` 和 `parallel()` 组合操作的嵌套深度没有强制限制。

## 用法

```js
const { series } = require('gulp');

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.build = series(javascript, css);
```

## 签名

```js
series(...tasks)
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:------:|-------|
| tasks<br>**(required)** | function<br>string | 任意数量的任务函数都可以作为单独的参数传递。如果您以前注册过任务，可以使用字符串，但不建议这样做。|

### 返回值

A composed operation to be registered as a task or nested within other `series` and/or `parallel` compositions.

When the composed operation is executed, all tasks will be run sequentially. If an error occurs in one task, no subsequent tasks will be run.

### Errors

When no tasks are passed, throws an error with the message, "One or more tasks should be combined using series or parallel".

When invalid tasks or unregistered tasks are passed, throws an error with the message, "Task never defined".

## Forward references

A forward reference is when you compose tasks, using string references, that haven't been registered yet. This was a common practice in older versions, but this feature was removed to achieve faster task runtime and promote the use of named functions.

In newer versions, you'll get an error, with the message "Task never defined", if you try to use forward references. You may experience this when trying to use `exports` for your task registration *and* composing tasks by string. In this situation, use named functions instead of string references.

During migration, you may need to use the [forward reference registry][undertaker-forward-reference-external]. This will add an extra closure to every task reference and dramatically slow down your build. **Don't rely on this fix for very long**.

## 避免重复任务

当运行组合操作时，每个任务将在每次提供时执行。

在两个不同的组合中引用的 `clean` 任务将运行两次，将导致不期望的结果。相反，在最终组合中指定的 `clean` 任务。

If you have code like this:
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
