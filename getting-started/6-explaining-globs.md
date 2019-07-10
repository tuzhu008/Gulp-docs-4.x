<!-- front-matter
id: explaining-globs
title: Explaining Globs
hide_title: true
sidebar_label: Explaining Globs
-->

# Explaining Globs

glob 是一串用于匹配文件路径的字符串和/或通配符。Globbing 是使用一个或多个 globs 在文件系统上定位文件的操作。

`src()` 方法需要一个 glob 字符串或一个 globs 数组来确定管道将操作哪些文件。必须为您的 glob(s) 找到至少一个匹配项，否则 `src()` 将出错。当使用一个 globs 数组时，它们按数组顺序匹配—对于负 globs 尤其有用。

## Segments and separators

片段就是分隔符之间的所有东西。glob 中的分隔符始终是 `/` 字符——无论操作系统如何——即使在路径分隔符为 `\\` 的 Windows 中也是如此。在 glob 中，`\\` 保留为转义字符。

在这里，`*` 被转义，因此它被视为一个文字而不是通配符。

```js
'glob_with_uncommon_\\*_character.js'
```

避免使用 Node 的 `path` 方法，比如 `path.join` 来创建 globs。在 Windows 上，它会生成一个无效的glob，因为Node使用 `\\` 作为分隔符。出于同样的原因，还要避免使用 `__dirname` 全局变量、`__filename` 全局变量或 `process.cwd()`。

```js
const invalidGlob = path.join(__dirname, 'src/*.js');
```

## 特殊字符: * (single-star)

匹配单个片段中的任何数量(包括 none)字符。适用于在一个目录中 globbing 文件。

这个 glob 将匹配 `index.js` 这样的文件，而不是像 `scripts/index.js` 或者 `scripts/nested/index.js`。

```js
'*.js'
```

## 特殊字符: ** (double-star)


跨片段段匹配任意数量的字符(包括 none)。适用于在嵌套目录中 globbing 文件。确保适当地限制双星型 globs，以避免不必要地匹配大型目录。

在这里，glob 被适当地限制在 `scripts/` 目录中。它将匹配 `scripts/index.js`、`scripts/nested/index.js` 和 `scripts/nested/twice/index.js`。

```js
'scripts/**/*.js'
```

<small>在前面的例子中，如果 `scripts/` 没有前缀，`node_modules` 或其他目录中的所有依赖项也将匹配。</small>

## 特殊字符: ! (negative)

因为 globs 是按数组顺序匹配的，所以一个负的 glob 必须跟在数组中至少一个非负的 glob 后面。首先找到一组匹配项，然后负的 glob 删除这些结果的一部分。当排除目录中的所有文件时，必须在目录名后面添加 `/**`，globbing 库在内部对其进行优化。

```js
['scripts/**/*.js', '!scripts/vendor/**']
```

如果任何非负的 globs 后面跟着一个负数，则不会从后面的匹配集中删除任何内容。

```js
['scripts/**/*.js', '!scripts/vendor/**', 'scripts/vendor/react.js']
```

负 globs 可作为限制双星 globs 的另一种选择。

```js
['**/*.js', '!node_modules/**']
```

<small>在前面的例子中，如果负的 glob 是 `!node_modules/**/*.js`，globbing 库不会优化负数，每一个匹配都必须与负的 glob 进行比较，这将非常慢。要忽略目录中的所有文件，只需在目录名后面添加 `/**` glob。</small>

## Overlapping globs

Two or more globs that (un)intentionally match the same file are considered overlapping. When overlapping globs are used within a single `src()`, gulp does its best to remove the duplicates, but doesn't attempt to deduplicate across separate `src()` calls.

## Advanced resources

Most of what you'll need to work with globs in gulp is covered here. If you'd like to get more in depth, here are a few resources.

* [Micromatch Documentation][micromatch-docs]
* [node-glob's Glob Primer][glob-primer-docs]
* [Begin's Globbing Documentation][begin-globbing-docs]
* [Wikipedia's Glob Page][wikipedia-glob]

[micromatch-docs]: https://github.com/micromatch/micromatch
[glob-primer-docs]: https://github.com/isaacs/node-glob#glob-primer
[begin-globbing-docs]: https://github.com/begin/globbing#what-is-globbing
[wikipedia-glob]: https://en.wikipedia.org/wiki/Glob_(programming)
