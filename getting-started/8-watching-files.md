<!-- front-matter
id: watching-files
title: Watching Files
hide_title: true
sidebar_label: Watching Files
-->

# 监听文件

`watch()` API使用文件系统监听程序将 [globs][globs-docs] 连接到 [tasks][creating-tasks-docs]。它监听与 globs 匹配的文件的更改，并在发生更改时执行任务。如果任务没有发出 [Async Completion][async-completion-doc] 信号，它将永远不会第二次运行。

这个 API 提供了内置的延迟和基于最常用默认值的队列。

```js
const { watch, series } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.default = function() {
  // You can use a single task
  watch('src/*.css', css);
  // Or a composed task
  watch('src/*.js', series(clean, javascript));
};
```

## 警告: 避免同步

监听器的任务不能是同步的，就像注册到任务系统中的任务。如果传递同步任务，则无法确定完成情况，任务将不会再次运行—该任务被假定仍在运行。

没有提供错误或警告消息，因为文件监视程序使您的 Node 进程保持运行。由于进程不退出，因此无法确定任务是完成了，还是仅仅运行了非常非常长的时间。

## 监听事件

默认情况下，每当创建、更改或删除文件时，监听器都会执行任务。

如果需要使用不同的事件，可以在调用 `watch()` 时使用 `events` 选项。可用的事件有 `'add'`、`'addDir'`、`'change'`、`'unlink'`、`'unlinkDir'`、`'ready'`、`'error'`。此外，`'all'` 是可用的，它表示除了 `'ready'` ”和 `'error'` 之外的所有事件。

```js
const { watch } = require('gulp');

exports.default = function() {
  // All events will be watched
  watch('src/*.js', { events: 'all' }, function(cb) {
    // body omitted
    cb();
  });
};
```

## 初始化执行

调用 `watch()` 时，任务不会执行，而是等待第一个文件更改。

要在第一个文件更改之前执行任务，请将 `ignoreInitial` 选项设置为 `false`。

```js
const { watch } = require('gulp');

exports.default = function() {
  // The task will be executed upon startup
  watch('src/*.js', { ignoreInitial: false }, function(cb) {
    // body omitted
    cb();
  });
};
```

## 队列

Each `watch()` guarantees that its currently running task won't execute again concurrently. When a file change is made while a watcher task is running, another execution will queue up to run when the task finishes. Only one run can be queued up at a time.

To disable queueing, set the `queue` option to `false`.

```js
const { watch } = require('gulp');

exports.default = function() {
  // The task will be run (concurrently) for every change made
  watch('src/*.js', { queue: false }, function(cb) {
    // body omitted
    cb();
  });
};
```

## Delay

Upon file change, a watcher task won't run until a 200ms delay has elapsed. This is to avoid starting a task too early when many files are being changed at once - like find-and-replace.

To adjust the delay duration, set the `delay` option to a positive integer.

```js
const { watch } = require('gulp');

exports.default = function() {
  // The task won't be run until 500ms have elapsed since the first change
  watch('src/*.js', { delay: 500 }, function(cb) {
    // body omitted
    cb();
  });
};
```

## Using the watcher instance

You likely won't use this feature, but if you need full control over changed files - like access to paths or metadata - use the [chokidar][chokidar-module-package] instance returned from `watch()`.

__Be careful:__ The returned chokidar instance doesn't have queueing, delay, or async completion features.

## Optional dependency

Gulp has an optional dependency called [fsevents][fsevents-package], which is a Mac-specific file watcher. If you see an installation warning for fsevents - _"npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents"_ - it is not an issue.
If fsevents installation is skipped, a fallback watcher will be used and any errors occurring in your gulpfile aren't related to this warning.

[globs-docs]: ../getting-started/6-explaining-globs.md
[creating-tasks-docs]: ../getting-started/3-creating-tasks.md
[async-completion-doc]: ../getting-started/4-async-completion.md
[chokidar-module-package]: https://www.npmjs.com/package/chokidar
[fsevents-package]: https://www.npmjs.com/package/fsevents
