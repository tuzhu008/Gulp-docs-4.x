<!-- front-matter
id: quick-start
title: Quick Start
hide_title: true
sidebar_label: Quick Start
-->

# 快速入门

如果您以前在全局范围内安装了gulp，
请在遵循下面这些指令之前运行 `npm rm --global gulp`。
有关更多信息，请阅读此 [Sip][sip-article]。

## 检查 node, npm, and npx
```sh
node --version
```
![Output: v8.11.1][img-node-version-command]
```sh
npm --version
```
![Output: 5.6.0][img-npm-version-command]
```sh
npx --version
```
![Output: 9.7.1][img-npx-version-command]

If they are not installed, follow the instructions [here][node-install].

## 安装 gulp 命令行实用程序
```sh
npm install --global gulp-cli
```

## 创建一个项目目录并导航到其中
```sh
npx mkdirp my-project
```
```sh
cd my-project
```

## 创建一个 package.json 到项目目录
```sh
npm init
```
这将指导您为项目提供名称、版本、描述等。

## 安装 gulp 包到开发依赖中
```sh
npm install --save-dev gulp
```

## 验证 gulp 版本

```sh
gulp --version
```

确保输出与下面的屏幕截图匹配，否则可能需要重新执行本指南中的步骤。

![Output: CLI version 2.0.1 & Local version 4.0.0][img-gulp-version-command]

## 创建一个 gulpfile

使用文本编辑器，在项目根目录中创建一个名为 gulpfile.js 的文件，内容如下:
```js
function defaultTask(cb) {
  // 在这里放置默认任务的代码
  cb();
}

exports.default = defaultTask
```

## 测试

在项目目录中运行gulp命令:
```sh
gulp
```
要运行多个任务，可以使用 `gulp <task> <othertask>`.

## 结果

这个默认任务什么都没做。
![Output: Starting default & Finished default][img-gulp-command]

[sip-article]: https://medium.com/gulpjs/gulp-sips-command-line-interface-e53411d4467
[node-install]: https://nodejs.org/en/
[img-node-version-command]: https://gulpjs.com/img/docs-node-version-command.png
[img-npm-version-command]: https://gulpjs.com/img/docs-npm-version-command.png
[img-npx-version-command]: https://gulpjs.com/img/docs-npx-version-command.png
[img-gulp-version-command]: https://gulpjs.com/img/docs-gulp-version-command.png
[img-gulp-command]: https://gulpjs.com/img/docs-gulp-command.png
