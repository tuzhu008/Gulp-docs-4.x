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

When no tasks are passed, throws an error with the message, "One or more tasks should be combined using series or parallel".

When invalid tasks or unregistered tasks are passed, throws an error with the message, "Task never defined".

## Forward references

A forward reference is when you compose tasks, using string references, that haven't been registered yet. This was a common practice in older versions, but this feature was removed to achieve faster task runtime and promote the use of named functions.

In newer versions, you'll get an error, with the message "Task never defined", if you try to use forward references. You may experience this when trying to use `exports` for task registration _and_ composing tasks by string. In this situation, use named functions instead of string references.

During migration, you may need the [forward reference registry][undertaker-forward-reference-external]. This will add an extra closure to every task reference and dramatically slow down your build. **Don't rely on this fix for very long**.

## Avoid duplicating tasks

When a composed operation is run, each task will be executed every time it was supplied.

A `clean` task referenced in two different compositions would be run twice and lead to undesired results. Instead, refactor the `clean` task to be specified in the final composition.

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
