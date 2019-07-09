<!-- front-matter
id: async-completion
title: Async Completion
hide_title: true
sidebar_label: Async Completion
-->

# 异步实现

Node 以多种方式处理异步性。最常见的模式是[错误优先回调][node-api-error-first-callbacks]，
但也可能遇到 [streams][stream-docs]、[promises][promise-docs]、[event emitters][event-emitter-docs]、[child processes][child-process-docs]、
或者[observables][observable-docs]。Gulp 任务将所有这些类型的异步性规范化。

## 单任务实现

当任务返回一个 stream、promise、event emitter、child process、或者 observable 时，成功或错误提示 gulp 是继续还是结束。如果任务出错，gulp 将立即结束并显示该错误。

当使用 `series()` 组合任务时，错误将结束组合，不再执行其他任务。当使用 `parallel()` 组合任务时，一个错误将结束组合，但其他并行任务可能完成，也可能不完成。

### 返回 stream

```js
const { src, dest } = require('gulp');

function streamTask() {
  return src('*.js')
    .pipe(dest('output'));
}

exports.default = streamTask;
```

### 返回 promise

```js
function promiseTask() {
  return Promise.resolve('the value is ignored');
}

exports.default = promiseTask;
```

### 返回 event emitter

```js
const { EventEmitter } = require('events');

function eventEmitterTask() {
  const emitter = new EventEmitter();
  // Emit has to happen async otherwise gulp isn't listening yet
  setTimeout(() => emitter.emit('finish'), 250);
  return emitter;
}

exports.default = eventEmitterTask;
```

### 返回 child process

```js
const { exec } = require('child_process');

function childProcessTask() {
  return exec('date');
}

exports.default = childProcessTask;
```

### 返回 observable

```js
const { Observable } = require('rxjs');

function observableTask() {
  return Observable.of(1, 2, 3);
}

exports.default = observableTask;
```

### 使用错误有限回调

If nothing is returned from your task, you must use the error-first callback to signal completion. The callback will be passed to your task as the only argument - named `cb()` in the examples below.

```js
function callbackTask(cb) {
  // `cb()` should be called by some async work
  cb();
}

exports.default = callbackTask;
```

To indicate to gulp that an error occurred in a task using an error-first callback, call it with an `Error` as the only argument.

```js
function callbackError(cb) {
  // `cb()` should be called by some async work
  cb(new Error('kaboom'));
}

exports.default = callbackError;
```

However, you'll often pass this callback to another API instead of calling it yourself.

```js
const fs = require('fs');

function passingCallback(cb) {
  fs.access('gulpfile.js', cb);
}

exports.default = passingCallback;
```

## No synchronous tasks

Synchronous tasks are no longer supported. They often led to subtle mistakes that were hard to debug, like forgetting to return your streams from a task.

When you see the _"Did you forget to signal async completion?"_ warning, none of the techniques mentioned above were used. You'll need to use the error-first callback or return a stream, promise, event emitter, child process, or observable to resolve the issue.

## Using async/await

When not using any of the previous options, you can define your task as an [`async` function][async-await-docs], which wraps your task in a promise. This allows you to work with promises synchronously using `await` and use other synchronous code.

```js
const fs = require('fs');

async function asyncAwaitTask() {
  const { version } = fs.readFileSync('package.json');
  console.log(version);
  await Promise.resolve('some result');
}

exports.default = asyncAwaitTask;
```

[node-api-error-first-callbacks]: https://nodejs.org/api/errors.html#errors_error_first_callbacks
[stream-docs]: https://nodejs.org/api/stream.html#stream_stream
[promise-docs]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises
[event-emitter-docs]: https://nodejs.org/api/events.html#events_events
[child-process-docs]: https://nodejs.org/api/child_process.html#child_process_child_process
[observable-docs]: https://github.com/tc39/proposal-observable/blob/master/README.md
[async-await-docs]: https://developers.google.com/web/fundamentals/primers/async-functions
