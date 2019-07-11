<!-- front-matter
id: concepts
title: API Concepts
hide_title: true
sidebar_label: Concepts
-->

# 概念


以下概念是理解 API 文档的预备知识。它们将在整个过程中被引用，详细说明请参阅本页面。

如果你是新手，就从 [入门指南][quick-start-docs] 开始。

## Vinyl

Vinyl 是描述文件的元数据对象。Vinyl 实例的主要属性是文件系统中文件核心的 `path` 和 `contents` 核心方面。Vinyl 对象可用于描述来自多个源的文件（本地文件系统或任何远程存储选项上）。

## Vinyl 适配器

Vinyl 提供了一种描述文件的方法，但是需要一种访问这些文件的方法。使用 Vinyl 适配器访问每个文件源。

适配器暴露了：

* 一个签名为 `src(globs, [options])` 的方法，返回一个生成 Vinyl 对象的流。
* 一个带有签名为 `dest(folder, [options])` 的方法，返回一个使用 Vinyl 对象的流。
* 任何特定于其输入/输出媒体的额外方法-例如 `symlink` 方法 `vinyl-fs` 所提供的。它们应该总是返回产生和/或消耗 Vinyl 对象的流。


## Tasks

每个 gulp 任务都是一个异步 JavaScript 函数，它要么接受一个错误优先回调，要么返回一个流、promise、事件发射器、子进程或observable。由于一些平台限制，不支持同步任务。

有关更详细的解释，请参见[创建任务][creating-tasks-doc]。

## Globs

glob 是一串文字和/或通配符，如 `*`, `**`, 或 `!`，用于匹配文件路径。Globbing 是使用一个或多个 globs 在文件系统上定位文件的操作。

如果您没有使用globs的经验，请参阅。[解释 globs][explaining-globs-docs]

## Glob base

glob base (有时称为 glob parent)是 glob 字符串中任何特殊字符之前的路径段。因此，`/src/js/**.js` 的 blob base 是 `/src/js/`。所有匹配 glob 的路径都保证共享 glob base——该路径段不能是可变的。

由 `src()` 生成的 Vinyl 实例是用 glob base 集作为它们的 `base` 属性构造的。当使用 `dest()` 写入文件系统时，将从输出路径中删除 `base` ，以保留目录结构。

有关更深入的信息，请参阅 [glob-parent][glob-parent-external] 库。

## 文件系统统计数据

文件元数据作为 Node 的 [`fs.Stats`][fs-stats-external] 实例提供。它是 Vinyl 实例的 `stat`  属性，并在内部用于确定 Vinyl 对象是否表示目录或符号链接（symbolic link）。当写入文件系统时，权限和时间值将从 Vinyl 对象的 `stat`  属性同步。

## 文件系统模式

文件系统模式决定文件的权限。文件系统上的大多数文件和目录将具有相当宽松的模式，允许 gulp 代表您读取/写入/更新文件。默认情况下，gulp 将创建与运行进程具有相同权限的文件，但是您可以通过 `src()`、 `dest()` 中的选项配置模式。如果您遇到权限(EPERM)问题，请检查文件上的模式。

## 模块

Gulp 由许多小模块组成，这些模块被拉到一起以实现内聚性工作。通过在小模块中使用 [semver][semver-external]，我们可以在不发布 gulp 新版本的情况下发布 bug 修复和特性。通常，当您没有看到主存储库上的进展时，工作是在其中一个模块中完成的。

如果遇到问题，请使用 `npm update` 命令更新当前模块。如果问题仍然存在，则在单个项目存储库上打开一个 issue。

* [undertaker][undertaker-external] - the task registration system
* [vinyl][vinyl-external] - the virtual file objects
* [vinyl-fs][vinyl-fs-external] - a vinyl adapter to your local file system
* [glob-watcher][glob-watcher-external] - the file watcher
* [bach][bach-external] - task orchestration using `series()` and `parallel()`
* [last-run][last-run-external] - tracks the last run time of a task
* [vinyl-sourcemap][vinyl-sourcemap-external] - built-in sourcemap support
* [gulp-cli][gulp-cli-external] - the command line interface for interacting with gulp


[quick-start-docs]: ../getting-started/1-quick-start.md
[creating-tasks-doc]: ../getting-started/3-creating-tasks.md
[explaining-globs-docs]: ../getting-started/6-explaining-globs.md
[undertaker-external]: https://github.com/gulpjs/undertaker
[vinyl-external]: https://github.com/gulpjs/vinyl
[vinyl-fs-external]: https://github.com/gulpjs/vinyl-fs
[glob-watcher-external]: https://github.com/gulpjs/glob-watcher
[bach-external]: https://github.com/gulpjs/bach
[last-run-external]: https://github.com/gulpjs/last-run
[vinyl-sourcemap-external]: https://github.com/gulpjs/vinyl-sourcemap
[gulp-cli-external]: https://github.com/gulpjs/gulp-cli
[semver-external]: https://semver.org
[fs-stats-external]: https://nodejs.org/api/fs.html#fs_class_fs_stats
[glob-parent-external]: https://github.com/es128/glob-parent
