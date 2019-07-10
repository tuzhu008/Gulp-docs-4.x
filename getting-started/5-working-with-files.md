<!-- front-matter
id: working-with-files
title: Working with Files
hide_title: true
sidebar_label: Working with Files
-->

# 处理文件

gulp 提供了 `src()` 和 `dest()` 方法与计算机上的文件进行交互。

`src()` 是一个 [glob][explaining-globs-docs] 用来从文件系统进行读取，并产生一个 [Node 流][node-streams-docs]。它定位所有匹配的文件并将它们通过流的方式读入内存以。

`src()` 生成的流应该从任务返回，以表示异步完成，如 [创建任务][创建任务-文档] 中所述。

```js
const { src, dest } = require('gulp');

exports.default = function() {
  return src('src/*.js')
    .pipe(dest('output/'));
}
```

流的主要 API 是 `.pipe()` 方法，用于链式转换流或可写流。

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(dest('output/'));
}
```
`dest()` 的参数为一个输出目录字符串，它生成一个 [Node 流][Node-streams-docs]，通常用作终止流。当它接收到通过管道传递的文件时，它将这些文件内容和其他细节写到给定目录下的文件系统中。`symlink()` 方法也是可用的，其操作类似于 `dest()`，但它创建的是链接而不是文件(详细信息请参阅 [`symlink()`][symlink-api-docs])。

大多数情况下，插件将被使用 `.pipe()` 方法放置在 `src()` 和 `dest()`之间，并转换流中的文件。

## 向流中添加文件

还可以将 `src()` 放在管道中间，根据给定的 globs 向流添加文件。附加文件只对流中稍后的转换可用。如果 [globs 重叠][overlapping-globs-docs]，将再次添加文件。

在将纯 JavaScript 文件添加到管道并压缩（uglify）所有内容之前，这对于转换一些文件非常有用。

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(src('vendor/*.js'))
    .pipe(uglify())
    .pipe(dest('output/'));
}
```

## 输出阶段

`dest()` 可以在管道的中间使用，将中间状态写入文件系统。当接收到一个文件时，将当前状态写入文件系统，更新路径以表示输出文件的新位置，然后该文件继续沿着管道运行。

此特性对于创建具有相同管道的非缩小文件和缩小文件非常有用。

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');
const rename = require('gulp-rename');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(src('vendor/*.js'))
    .pipe(dest('output/'))
    .pipe(uglify())
    .pipe(rename({ extname: '.min.js' }))
    .pipe(dest('output/'));
}
```

## Modes: streaming, buffered, and empty

`src()` can operate in three modes: buffering, streaming, and empty. These are configured with the `buffer` and `read` [options][src-options-api-docs] on `src()`.

* Buffering mode is the default and loads the file contents into memory. Plugins usually operate in buffering mode and many don't support streaming mode.
* Streaming mode exists mainly to operate on large files that can't fit in memory, like giant images or movies. The contents are streamed from the filesystem in small chunks instead of loaded all at once. If you need to use streaming mode, look for a plugin that supports it or write your own.
* Empty mode contains no contents and is useful when only working with file metadata.

[explaining-globs-docs]: ../getting-started/6-explaining-globs.md
[creating-tasks-docs]: ../getting-started/3-creating-tasks.md
[overlapping-globs-docs]: ../getting-started/6-explaining-globs.md#overlapping-globs
[node-streams-docs]: https://nodejs.org/api/stream.html
[symlink-api-docs]: ../api/symlink.md
[src-options-api-docs]: ../api/src.md#options
