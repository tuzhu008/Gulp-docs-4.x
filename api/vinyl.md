<!-- front-matter
id: vinyl
title: Vinyl
hide_title: true
sidebar_label: Vinyl
-->

# Vinyl

虚拟的文件格式。当 `src()` 读取文件时，将生成一个 Vinyl 对象来表示文件——包括路径、内容和其他元数据。

Vinyl 对象可以使用[插件][using-plugins-docs]进行转换。还可以使用 `dest()` 将它们持久化到文件系统。

当创建您自己的 Vinyl 对象时——而不是使用 `src()` 生成——使用外部 `vinyl` 模块，如下面的用法所示。

## 用法

```js
const Vinyl = require('vinyl');

const file = new Vinyl({
  cwd: '/',
  base: '/test/',
  path: '/test/file.js',
  contents: new Buffer('var x = 123')
});

file.relative === 'file.js';

file.dirname === '/test';
file.dirname = '/specs';
file.path === '/specs/file.js';

file.basename === 'file.js';
file.basename = 'file.txt';
file.path === '/specs/file.txt';

file.stem === 'file';
file.stem = 'foo';
file.path === '/specs/foo.txt';
file.extname === '.txt';
file.extname = '.js';
file.path === '/specs/file.js';
```

## 签名

```js
new Vinyl([options])
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:------:|-------|
| options | object | 详情请参加下文 [选项][options-section]。 |

### 返回值

返回一个 Vinyl 类的实例，表示一个单独的虚拟文件，详见下面的 [Vinyl 实例][vinyl-instance-section]。

### Errors

当传递的任何选项都不符合表中定义的[实例属性定义][instance-properties-section](如 `path` 被设置为一个数字)时抛出错误。

### 选项

| name | type | default | note |
|:-------:|:------:|-----------|--------|
| cwd | string | `process.cwd()` | 将从中推导相对路径的目录。将被[规范化](normalization-and-concatenation-section)，并删除尾随分隔符。|
| base | string | | 用于计算 `relative` 实例属性。 如果没有设置，则回退到 `cwd` 的值。通常设置为 [glob base][glob-base-concepts]。将被[规范化](normalization-and-concatenation-section)，并删除尾随分隔符。|
| path | string | | 完整的绝对文件路径。将被[规范化](normalization-and-concatenation-section)，并删除尾随分隔符。 |
| history | array | `[ ]` | 预先填充 Vinyl 实例的 `history` 的路径数组。通常来自于从以前的 Vinyl 对象派生出一个新的 Vinyl 对象。如果 `path` 和 `history` 都被传递，`path` 将被附加到 `history` 中。每一项都将被[规范化](normalization-and-concatenation-section)，并删除尾随分隔符。  |
| stat | object | | 一个 `fs.Stats` 实例，通常是对文件调用 `fs.stat()` 的结果。用于确定 Vinyl 对象是否表示目录或符号链接。 |
| contents | ReadableStream<br>Buffer<br>`null` | `null` | 文件的内容。如果 `contents` 是一个 ReadableStream，它将被包装在一个 [可克隆可读的][cloneable-readable-external] 流中。|

`options` 上的任何其他属性都将直接分配给 Vinyl 实例。

```js
const Vinyl = require('vinyl');

const file = new Vinyl({ foo: 'bar' });
file.foo === 'bar';
```

## Vinyl 实例

每个 Vinyl 对象实例都具有访问和/或修改虚拟文件信息的属性和方法。

### 实例属性

所有内部管理的路径——除了 `contents` 和 `stat` 之外的任何实例属性——都被规范化，并删除了末尾分隔符。有关更多信息，请参见[规范化和连接][normalization-and-concatenation-section]。

| 属性 | 类型 | 描述 | 抛出错误 |
|:-----------:|:------:|----------------|----------|
| contents | ReadableStream<br>Buffer<br>`null` |  获取和设置虚拟文件的内容。如果将其设置为 ReadableStream，它将被包装在一个 [可克隆可读的][cloneable-readable-external] 流中。| 如果设置为 ReadableStream， Buffer，或者 `null` 之外的任何值 |
| stat | object | 获取或设置 [`fs.Stats`][fs-stats-concepts] 的实例。当确定 Vinyl 对象是否表示目录或符号链接时使用。 | |
| cwd | string | 获取并设置当前工作目录。用于推导相对路径。 | 如果设置为空字符串或任何非字符串值。 |
| base | string | 获取和设置基目录。用于计算 `relative` 实例属性。 在由 `src()` 生成的 Vinyl 对象上，将设置为 [glob base][global -base-concepts]。如果设置为 `null` 或 `undefined`，则会退到 `cwd` 实例属性的值。 |如果设置为空字符串或任何非字符串值(`null` 或 `undefined` 除外)。|
| path | string | 获取和设置完整的绝对文件路径。设置为与当前 `path` 不同的值会将新路径附加到 `history` 实例属性中。 | 如果设置为任何非字符串值。 |
| history | array | 已分配的 Vinyl 对象的所有 `path` 值的数组。第一个元素是原始路径，最后一个元素是当前路径。此属性及其元素应被视为只读，仅通过设置 `path`实例属性间接更改。| | |
| relative | string | 获取 `base` 和 `path` 实例属性之间的相对路径段。| 如果设置为任何值。如果在 `path` 不可用时访问。 |
| dirname | string | 获取和设置 `path` 实例属性的目录。 | 如果在 `path` 不可用时访问 |
| stem | string | 获取并设置 `path` 实例属性的 stem（没有扩展的文件名）。 | 如果在 `path` 不可用时访问 |
| extname | string | 获取并设置 `path` 实例属性的扩展名。 | 如果在 `path` 不可用时访问 |
| basename | string | 获取和设置 `path` 实例属性的文件名(`stem + extname`)。 | 如果在 `path` 不可用时访问 |
| symlink | string | 获取和设置符号链接的引用路径。 | 如果设置为任何非字符串值。 |

### 实例方法

| 方法 | 返回值类型 | 返回值 |
|:----------:|:--------------:|--------|
| `isBuffer()` | boolean | 如果 `contents` 实例属性是一个 Buffer，则返回 true。|
| `isStream()` | boolean |  如果 `contents` 实例属性是一个 Stream，则返回 true。|
| `isNull()` | boolean | 如果 `contents` 实例属性为 `null`，则返回 true。|
| `isDirectory()` | boolean | 如果实例表示一个目录，则返回 true。当 `isNull()` 返回 true，`stat` 实例属性是一个对象，并且  `stat.isDirectory()` 返回 true 时，实例被认为是一个目录。这假设 Vinyl 对象是用一个有效的（或适当模拟的） `fs.Stats` 对象构造的。
| `isSymbolic()` | boolean | 如果实例表示符号链接，则返回 true。 当 `isNull()` 返回 true，`stat` 实例属性是一个对象，并且 `stat.isSymbolicLink()` 返回 true 时， 实例被认为是 symbolic。 这假设 Vinyl 对象是用一个有效的（或适当模拟的） `fs.Stats` 对象构造的。|
| `clone([options])` | object | A new Vinyl object with all properties cloned. 一个使用所有属性克隆出的新的 Vinyl 对象。 默认情况下，自定义属性是深拷贝。如果 `deep` 选项为 false，自定义属性将被浅拷贝。如果 `contents` 选项设置为 fasle 并且 `contents` 属性是一个 Buffer，那么这个 Buffer 将被复用，而不是克隆。 |
| `inspect()` | string | 返回 Vinyl 对象的格式化说明。由 Node 的 console.log 自动调用。 |

## Normalization and concatenation

All path properties are normalized by their setters. Concatenate paths with `/`, instead of using `path.join()`, and normalization will occur properly on all platforms. Never concatenate with `\` - it  is a valid filename character on POSIX system.

```js
const file = new File();
file.path = '/' + 'test' + '/' + 'foo.bar';

console.log(file.path);
// posix => /test/foo.bar
// win32 => \\test\\foo.bar
```

[options-section]: #options
[vinyl-instance-section]: #vinyl-instance
[instance-properties-section]: #instance-properties
[normalization-and-concatenation-section]: #normalization-and-concatenation
[glob-base-concepts]: ../api/concepts.md#glob-base
[fs-stats-concepts]: ../api/concepts.md#file-system-stats
[using-plugins-docs]: ../getting-started/7-using-plugins.md
[cloneable-readable-external]: https://github.com/mcollina/cloneable-readable
