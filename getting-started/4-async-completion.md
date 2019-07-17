<!-- front-matter
id: async-completion
title: Async Completion
hide_title: true
sidebar_label: Async Completion
-->

# 异步完成

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

### 使用错误优先回调

如果任务没有返回任何内容，则必须使用错误优先回调函数来表示完成。在下面的示例中，回调函数将作为惟一的—名为 `cb()` 的参数传递给任务函数。

```js
function callbackTask(cb) {
  // `cb()` should be called by some async work
  cb();
}

exports.default = callbackTask;
```

要使用错误有限回调来指示 gulp 任务中发生了错误，请使用 `Error` 作为惟一参数调用它。

```js
function callbackError(cb) {
  // `cb()` should be called by some async work
  cb(new Error('kaboom'));
}

exports.default = callbackError;
``

但是，经常将这个回调传递给另一个 API，而不是自己调用它。

```js
const fs = require('fs');

function passingCallback(cb) {
  fs.access('gulpfile.js', cb);
}

exports.default = passingCallback;
```

## 不再有同步任务

不再支持同步任务。它们经常导致难以调试的细微错误，比如忘记从任务中返回流。

当您看到 _"Did you forget to signal async completion?"_ 警告，证明上述技术均未使用。您将需要使用错误优先回调或返回流、promise、event emitter、child process 或 observable 来解决此问题。

## 使用 async/await

当不使用前面的任何选项时，可以将任务定义为[`async` 函数][async-await-docs]，它将任务封装在一个 promise 中。这允许您使用 `await` 同步处理 promise，并使用其他同步代码。

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
