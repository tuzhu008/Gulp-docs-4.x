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
`dest()` 的参数为一个输出目录字符串，它生成一个 [Node 流][Node-streams-docs]，通常用作终止流。

`dest()` is given an output directory string and also produces a [Node stream][node-streams-docs] which is generally used as a terminator stream. When it receives a file passed through the pipeline, it writes the contents and other details out to the filesystem at a given directory.  The `symlink()` method is also available and operates like `dest()`, but creates links instead of files (see [`symlink()`][symlink-api-docs] for details).

Most often plugins will be placed between `src()` and `dest()` using the `.pipe()` method and will transform the files within the stream.

## Adding files to the stream

`src()` can also be placed in the middle of a pipeline to add files to the stream based on the given globs. The additional files will only be available to transformations later in the stream.  If [globs overlap][overlapping-globs-docs], the files will be added again.

This can be useful for transpiling some files before adding plain JavaScript files to the pipeline and uglifying everything.

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

## Output in phases

`dest()` can be used in the middle of a pipeline to write intermediate states to the filesystem. When a file is received, the current state is written out to the filesystem, the path is updated to represent the new location of the output file, then that file continues down the pipeline.

This feature can be useful to create unminified and minified files with the same pipeline.

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
