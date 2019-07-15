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

**对于接受函数的选项，传递的函数将与每个 Vinyl 对象一起调用，并且必须返回另一个列出类型的值。**

| name | type | default | note |
|:-------:|:------:|-----------|-------|
| cwd | string<br>function | `process.cwd()` | 这个目录将与任何相对路径相结合以形成绝对路径。对于绝对路径忽略。用于避免将 `directory` 与 `path.join()` 相结合。|
| mode | number<br>function | `stat.mode` of the Vinyl object | 创建文件时使用的模式。如果没有设置，并且缺少 `stat.mode`，则使用 process' 模式。 |
| dirMode | number<br>function | | 创建目录时使用的模式。如果没有设置，将使用 process' 模式。 |
| overwrite | boolean<br>function | true | 如果为 true，则用相同的路径覆盖现有文件。 |
| append | boolean<br>function | false |  如果为 true，则将内容添加到文件末尾，而不是替换现有内容。|
| sourcemaps | boolean<br>string<br>function | false | 如果为 true，则将内联 sourcemaps 写入输出文件。指定一个 `string` 路径将在给定路径上写入外部 [sourcemaps][sourcemaps-section] 。 |
| relativeSymlinks | boolean<br>function | false | 当为false 时，创建的任何符号链接将是绝对的。<br>**注意**: 如果正在创建连接，则忽略它们，因为它们必须是绝对的。 |
| useJunctions | boolean<br>function | true | 此选项仅适用于 Windows，在其他地方被忽略。当为 true 时，创建目录符号链接作为连接（junction）。详情请见下文 [Symbolic links on Windows][symbolic-links-section] |

## 元数据更新

每当 `dest()` 流创建一个文件时，就会将 Vinyl 对象的 `mode`、`mtime` 和 `atime` 与创建的文件进行比较。如果它们不同，创建的文件将被更新以反映 Vinyl 对象的元数据。如果这些属性相同，或者 gulp 没有更改的权限，则会跳过该尝试。

在不支持 Node 的 `process.getuid()`或 `process.geteuid()` 方法的 Windows 或其他操作系统上禁用此功能。这是因为Windows通过使用 `fs.fchmod()` 和 `fs.futimes() 会产生意想不到的结果。

**注意**: `fs.futimes()` 在内部将 `mtime` 和 `atime` 时间戳转换为秒。这种除以 1000 的方法可能会导致 32 位操作系统的精度有所下降。

## Sourcemaps

Sourcemap 支持直接构建到 `src()` 和 `dest()` 中，但默认情况下是禁用的。使其能够生成内联或外部 sourcemaps。

内联 sourcemaps:

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: true }));
```

外部 sourcemaps:

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: '.' }));
```

## Symbolic links on Windows

在 Windows 上创建符号链接时，`type` 参数被传递给 Node 的 `fs.symlink()` 方法，该方法指定被链接的目标的类型。链接类型设置为:

* `'file'`，当目标是一个常规文件时
* `'junction'`， 当目标是一个目录时
* `'dir'`， 当目标是一个目录并且用户禁用了 `useJunctions` 选项时


如果试图创建 dangling (指向不存在的目标)链接，则无法自动确定链接类型。在这些情况下，根据 dangling 链接是通过 `symlink()` 创建的还是通过 `dest()` 创建的，行为会有所不同。

对于通过 `symlink()` 创建的 dangling 链接，传入的 Vinyl 对象表示目标，因此其 stats 将确定所需的链接类型。如果 `isDirectory()`  返回 false，则创建一个 `'file'` 链接，否则根据 `useJunctions` 选项的值创建一个 `'junction'` 或 `'dir'` 链接。

对于通过 `dest()` 创建的 dangling 链接，传入的 Vinyl 对象表示链接——通常通过 `src(..., { resolveSymlinks: false })` 从磁盘加载。在这种情况下，无法合理地确定链接类型，默认使用 `'file'`。如果正在创建指向目录的 dangling 链接，这可能会导致意外行为。**避免这种情况。**

[sourcemaps-section]: #sourcemaps
[symbolic-links-section]: #symbolic-links-on-windows
[options-section]: #选项
[metadata-updates-section]: #metadata-updates
[vinyl-concepts]: ../api/concepts.md#vinyl
