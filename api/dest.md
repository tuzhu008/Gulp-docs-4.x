<!-- front-matter
id: dest
title: dest()
hide_title: true
sidebar_label: dest()
-->

# dest()

创建一个用于将 [Vinyl][vinyl-concepts] 对象写入到文件系统的流。

## 用法

```js
const { src, dest } = require('gulp');

function copy() {
  return src('input/*.js')
    .pipe(dest('output/'));
}

exports.copy = copy;
```

## 签名

```js
dest(directory, [options])
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:-----:|--------|
| directory<br>**(required)** | string<br>function | 将写入文件的输出目录的路径。如果使用一个函数，该函数将与每个 Vinyl 对象一起调用，并且必须返回一个字符串目录路径。|
| options | object | 详情见下文[选项][options-section]]。|

### 返回值

返回一个可以在管道的中间或末尾使用的流，用于在文件系统上创建文件。

每当 Vinyl 对象通过流被传递时，它将内容和其他细节写到给定目录下的文件系统。如果 Vinyl 对象具有 `symlink` 属性，将创建符号链接（symbolic link）而不是写入内容。创建文件后，将[更新其元数据][metadata-updates-section]以匹配 Vinyl 对象。

在文件系统上创建文件时，Vinyl 对象将被修改。
* `cwd`、`base` 和 `path` 属性将被更新以匹配创建的文件。
* `stat` 属性将被更新，以匹配文件系统上的文件。
* 如果 `contents` 属性是一个流，它将被重置，以便可以再次读取。

### Errors

当目录为空字符串时，将抛出一个错误，并提示 "Invalid dest() folder argument. Please specify a non-empty string or a function."（无效的 dest() 文件夹参数。请指定非空字符串或函数。）

当目录不是字符串或函数时，将抛出一个错误，并提示 "Invalid dest() folder argument. Please specify a non-empty string or a function."

当 `directory` 是一个返回空字符串或 `undefined` 的函数时，将发出一条错误消息 “Invalid output folder”。

### 选项


**For options that accept a function, the passed function will be called with each Vinyl object and must return a value of another listed type.**

| name | type | default | note |
|:-------:|:------:|-----------|-------|
| cwd | string<br>function | `process.cwd()` | The directory that will be combined with any relative path to form an absolute path. Is ignored for absolute paths. Use to avoid combining `directory` with `path.join()`. |
| mode | number<br>function | `stat.mode` of the Vinyl object | The mode used when creating files. If not set and `stat.mode` is missing, the process' mode will be used instead. |
| dirMode | number<br>function | | The mode used when creating directories. If not set, the process' mode will be used. |
| overwrite | boolean<br>function | true | When true, overwrites existing files with the same path. |
| append | boolean<br>function | false | If true, adds contents to the end of the file, instead of replacing existing contents. |
| sourcemaps | boolean<br>string<br>function | false | If true, writes inline sourcemaps to the output file. Specifying a `string` path will write external [sourcemaps][sourcemaps-section] at the given path. |
| relativeSymlinks | boolean<br>function | false | When false, any symbolic links created will be absolute.<br>**Note**: Ignored if a junction is being created, as they must be absolute. |
| useJunctions | boolean<br>function | true | This option is only relevant on Windows and ignored elsewhere. When true, creates directory symbolic link as a junction. Detailed in [Symbolic links on Windows][symbolic-links-section] below. |

## Metadata updates

Whenever the `dest()` stream creates a file, the Vinyl object's `mode`, `mtime`, and `atime` are compared to the created file. If they differ, the created file will be updated to reflect the Vinyl object's metadata. If those properties are the same, or gulp doesn't have permissions to make changes, the attempt is skipped silently.

This functionality is disabled on Windows or other operating systems that don't support Node's `process.getuid()` or `process.geteuid()` methods. This is due to Windows having unexpected results through usage of `fs.fchmod()` and `fs.futimes()`.

**Note**: The `fs.futimes()` method internally converts `mtime` and `atime` timestamps to seconds. This division by 1000 may cause some loss of precision on 32-bit operating systems.

## Sourcemaps

Sourcemap support is built directly into `src()` and `dest()`, but it is disabled by default. Enable it to produce inline or external sourcemaps.

Inline sourcemaps:
```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: true }));
```

External sourcemaps:
```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: '.' }));
```

## Symbolic links on Windows

When creating symbolic links on Windows, a `type` argument is passed to Node's `fs.symlink()` method which specifies the kind of target being linked. The link type is set to:
* `'file'` when the target is a regular file
* `'junction'` when the target is a directory
* `'dir'` when the target is a directory and the user disables the `useJunctions` option


If you try to create a dangling (pointing to a non-existent target) link, the link type can't be determined automatically. In these cases, behavior will vary depending on whether the dangling link is being created via `symlink()` or via `dest()`.

For dangling links created via `symlink()`, the incoming Vinyl object represents the target, so its stats will determine the desired link type. If `isDirectory()` returns false then a `'file'` link is created, otherwise a `'junction'` or a `'dir'` link is created depending on the value of the `useJunctions` option.

For dangling links created via `dest()`, the incoming Vinyl object represents the link - typically loaded from disk via `src(..., { resolveSymlinks: false })`. In this case, the link type can't be reasonably determined and defaults to using `'file'`. This may cause unexpected behavior if you are creating a dangling link to a directory. **Avoid this scenario.**

[sourcemaps-section]: #sourcemaps
[symbolic-links-section]: #symbolic-links-on-windows
[options-section]: #options
[metadata-updates-section]: #metadata-updates
[vinyl-concepts]: ../api/concepts.md#vinyl
