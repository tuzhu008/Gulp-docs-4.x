<!-- front-matter
id: src
title: src()
hide_title: true
sidebar_label: src()
-->

# src()

创建一个流，用于从文件系统读取 [Vinyl][vinyl-concepts] 对象。

**注：**BOMs(字节顺序标记)在 UTF-8 中没有任何作用，除非使用 `removeBOM` 选项禁用，否则 `src()` 将从读取的 UTF-8 文件中删除BOMs。

## 用法

```javascript
const { src, dest } = require('gulp');

function copy() {
  return src('input/*.js')
    .pipe(dest('output/'));
}

exports.copy = copy;
```


## 签名

```js
src(globs, [options])
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:------:|-------|
| globs | string<br>array | [Globs][globs-concepts] to watch on the file system. |
| options | object |在下面的[选项][options-section]中详细说明。|

### 返回值

返回一个可以在管道的开始或中间使用的流，用于根据给定的 globs 添加文件。

### Errors

当 `globs` 参数只能匹配一个文件(如 `foo/bar.js`)而且没有找到匹配时，会抛出一个错误，提示 "File not found with singular glob"。若要抑制此错误，请将 `allowEmpty` 选项设置为 `true`。

当在 `globs` 中给出一个无效的 glob 时，抛出一个错误，并显示 "Invalid glob argument"。


### 选项

**选项可以是一个函数，这个函数将在每一个 Vinyl 对象上调用，并且必须返回另一个列出的类型的值**

| 名称 | 类型 | 默认值 | 描述 |
|:--------:|:------:|------------|--------|
| buffer | boolean<br>function | true | 当为 true 时，文件内容被缓冲到内存中。如果为false，Vinyl 对象的 `contents` 属性将是一个暂停流。可能无法缓冲大文件的内容。<br>**注意:**插件可能不支持流媒体内容。|
| read | boolean<br>function | true | 如果为 false，文件将不会被读取，并且它们的 Vinyl 对象将不能通过 `.dest()` 写入磁盘。|
| since | date<br>timestamp<br>function | | 设置时，仅为自指定时间以来修改过的文件创建 Vinyl 对象。 |
| removeBOM | boolean<br>function | true | 如果为 true，则从 UTF-8 编码的文件中删除 BOM。如果为 false，则忽略 BOM。|
| sourcemaps | boolean<br>function | false | 如果为 true，则在创建的 Vinyl 对象上启用 [sourcemaps][sourcemaps-section]。加载内联 sourcemaps 并解析外部 sourcemap 链接。 |
| resolveSymlinks | boolean<br>function | true | true 时，递归地解析链接到目标的符号(symbolic)链接。如果为 false，则保留符号链接并将 Vinyl 对象的 `symlink` 属性设置为原始文件的路径。|
| cwd | string | `process.cwd()` | 将与任何相对路径相结合以形成绝对路径的目录。对于绝对路径忽略。用于避免将 globs 与 `path.join()` 相结合。<br>_此选项直接传递给 [glob-stream][glob-stream-external]。_|
| base | string | | 显式地在创建的 Vinyl 对象上设置 `base` 属性。详情请参见 [API Concepts][glob-base-concepts].<br>_此选项直接传递给 [glob-stream][glob-stream-external]。_|
| cwdbase | boolean | false | 如果为 true，`cwd` 和 `base` 选项应该对应起来。<br>_此选项直接传递给 [glob-stream][glob-stream-external]。_|
| root | string | | 解析 `globs` 的根路径.<br>_此选项直接传递给 [glob-stream][glob-stream-external]。_|
| allowEmpty | boolean | false | 当为 false 时，只能匹配一个文件的 `globs` (如 `foo/bar.js`)如果没有找到匹配的文件，就会引发一个错误。如果为 true 的，则不会报错。<br>_此选项直接传递给 [glob-stream][glob-stream-external]。_ |
| uniqueBy | string<br>function | `'path'` | 通过比较字符串属性名或函数的结果，从流中删除重复项。<br>**注意：**当使用函数时，函数接收流数据(对象包含 `cwd`、`base`、`path` 属性)。|
| dot | boolean | false | 如果为 true，请将 globs 与 `.gitignore` 等点文件进行比较。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| silent | boolean | true |如果为 true，则禁止在 `stderr` 上打印警告。<br>**注意:**  此选项直接传递给 [node-glob][node-glob-external]，但默认为 `true` 而不是 `false`。|
| mark | boolean | false | 如果为 true，将向目录匹配项追加一个 `/` 字符。通常不需要，因为路径是在管道中规范化的<br>_此选项直接传递给 [node-glob][node-glob-external]。_  |
| nosort | boolean | false | 如果为 true，禁用对 glob 结果排序。<br>_此选项直接传递给 [node-glob][node-glob-external]。_|
| stat | boolean | false | 如果为 true， `fs.stat()` 在所有的结果上被调用。这增加了额外的开销，通常不应该使用。<br>_此选项直接传递给 [node-glob][node-glob-external]。_|
| strict | boolean | false | 如果为 true，如果在尝试读取目录时遇到意外问题，将抛出错误。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| nounique | boolean | false | 当为 false 时，可以防止结果集中出现重复的文件。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| debug | boolean | false | If true, debugging information will be logged to the command line.<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| nobrace | boolean | false | 如果为 true， avoids expanding brace sets - e.g. `{a,b}` or `{1..3}`.<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| noglobstar | boolean | false | 如果为 true，treats double-star glob character as single-star glob character.<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| noext | boolean | false | 如果为 true，avoids matching [extglob][extglob-docs] patterns - e.g. `+(ab)`.<br>_此选项直接传递给 [node-glob][node-glob-external]。_|
| nocase | boolean | false | 如果为 true，则执行不区分大小写的匹配。<br>**注意:** 在不区分大小写的文件系统上，默认情况下，non-magic 模式将被匹配。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| matchBase | boolean | false | 如果true 并且 globs 不包含任何 `/` 字符，遍历所有目录并匹配该 glob ——  `*.js` 将被视为等同于 `**/*.js`。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| nodir | boolean | false | 若要仅匹配目录，glob 请以 `/` 结束。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| ignore | string<br>array | | 从匹配中排除。这个选项与否定的(negated) `globs` 组合在一起。<br>**注意:** 无论其他设置如何，这些 `globs` 总是与点文件匹配。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| follow | boolean | false | 如果为 true，在展开 `**` globs 时将遍历符号链接目录。<br>**注意:** 这可能导致循环链接出现问题。<br>_此选项直接传递给 [node-glob][node-glob-external]。_|
| realpath | boolean | false | 如果为 true，`fs.realpath()` 在所有的结果上调用。这可能导致悬挂式（dangling）链接。<br>_此选项直接传递给 [node-glob][node-glob-external]。_|
| cache | object | | 之前生成的缓存对象——避免了一些文件系统调用。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| statCache | object |  | 之前生成的 `fs.Stat` 缓存——避免了一些文件系统调用。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| symlinks | object | | 之前生成的符号链接缓存——避免了一些文件系统调用。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |
| nocomment | boolean | false | 当为 false 时，将 glob 开头的 `#` 字符视为注释。<br>_此选项直接传递给 [node-glob][node-glob-external]。_ |

## 资源映射

资源映射支持直接构建到 `src()` 和 `dest()` 中，但是默认情况下是禁用的。使其能够生成内联或外部资源映射。

内联资源映射:

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: true }));
```

外部资源映射：

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: '.' }));
```

[sourcemaps-section]: #sourcemaps
[options-section]: #options
[vinyl-concepts]: ../api/concepts.md#vinyl
[glob-base-concepts]: ../api/concepts.md#glob-base
[globs-concepts]: ../api/concepts.md#globs
[extglob-docs]: ../documentation-missing.md
[node-glob-external]: https://github.com/isaacs/node-glob
[glob-stream-external]: https://github.com/gulpjs/glob-stream
