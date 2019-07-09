<!-- front-matter
id: javascript-and-gulpfiles
title: JavaScript and Gulpfiles
hide_title: true
sidebar_label: JavaScript and Gulpfiles
-->

# JavaScript 和 Gulpfiles

Gulp 允许你使用已有的 JavaScript 知识来书写 gulpfiles 或者使用您对 gulpfiles 的经验编写简单的 JavaScript。
然提供了一些实用程序来简化文件系统和命令行，但是编写的其他所有内容都是纯 JavaScript。

## Gulpfile 说明

gulpfile 是项目目录下一个名为 `gulpfile.js` 文件（或 `Gulpfile.js`），当运行 `gulp` 时会自动加载它。在这个文件内部，经常会看到 gulp 的 API，如 `src()`, `dest()`, `series()`, or `parallel()` ，但是也可以使用任何 JavaScript 或者 Node 模块。任何导出的函数都将被注册到 gulp 的任务系统。

## 转译

可以使用需要转译的语言来编写 gulpfile，比如 TypeScript 或 Babel，需要修改 `gulpfile.js` 的扩展名并安装匹配的转译器模块。


* TypeScript，重命名为 `gulpfile.ts` 并安装 [ts-node][ts-node-module] 模块.
* Babel，重命名为 `gulpfile.babel.js` 并安装 [@babel/register][babel-register-module] 模块.

__大多数新版本的 node 支持 TypeScript 或 Babel 提供的大多数特性, 除了 `import`/`export` 语法。当只需要该语法时，将文件命名为 `gulpfile.esm.js` 并安装 [esm][esm-module] 模块。__

要更深入地了解这个主题和支持的扩展的完整列表，请参见 [gulpfile transpilation][gulpfile-transpilation-advanced] 文档。

##  分割 gulpfile

许多用户最开始会将所有逻辑添加到一个 gulpfile。如果它变得太大，可以重构成单独的文件。

每个任务都可以分割成单独的文件，然后导入到 gulpfile 中进行组合。这不仅可以保持任务的组织性，还允许您独立地测试每个任务，或者根据条件改变组合。

Node 的模块解析允许使用 `gulpfile.js` 文件夹来替换 `gulpfile.js`，只需要文件夹内包含一个 `index.js`，该文件将会被视为 `gulpfile.js`。然后，该目录可以包含用于任务的各个模块。如果使用了转译器，按此命名文件夹和文件。

> 译者注：如 TypeScript，命名如下：
  文件夹：gulpfile.ts
  文件：index.ts

[gulpfile-transpilation-advanced]: ../documentation-missing.md
[ts-node-module]: https://www.npmjs.com/package/ts-node
[babel-register-module]: https://www.npmjs.com/package/@babel/register
[esm-module]: https://www.npmjs.com/package/esm
