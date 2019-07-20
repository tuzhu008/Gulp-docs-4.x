## 简介

Gulp 的 [ESLint](https://eslint.org/) 插件，用于对 js 代码进行格式检查。

[github 地址](https://github.com/adametry/gulp-eslint)

## 安装

```
npm install --save-dev gulp-eslint
```

## 用法

```js
const {src, task} = require('gulp');
const eslint = require('gulp-eslint');

function defaultTask() {
  return src(['scripts/*.js'])
    // eslint() attaches the lint output to the "eslint" property
    // of the file object so it can be used by other modules.
    .pipe(eslint())
    // eslint.format() outputs the lint results to the console.
    // Alternatively use eslint.formatEach() (see Docs).
    .pipe(eslint.format())
    // To have the process exit with an error code (1) on
    // lint error, return the stream and pipe to failAfterError last.
    .pipe(eslint.failAfterError());
}
```

或者使用插件 API 做如下事情：

```js
gulp.src(['**/*.js','!node_modules/**'])
  .pipe(eslint({
    rules: {
      'my-custom-rule': 1,
      'strict': 2
    },
    globals: [
      'jQuery',
      '$'
    ],
    envs: [
      'browser'
    ]
  }))
  .pipe(eslint.formatEach('compact', process.stderr));
```

有关其他示例，请查看[示例目录](https://github.com/adametry/gulp-eslint/tree/master/example)。

## API

### eslint\(\)

没有显式配置。会相对于每个 linted 文件解析一个 `.eslintrc` 文件。

### eslint\(options\)

参见 [ESlint CLIEngine options](https://eslint.org/docs/developer-guide/nodejs-api#cliengine)。

#### options.rules

类型：`Object`

设置[规则](https://eslint.org/docs/rules/)的[配置](https://eslint.org/docs/user-guide/configuring#configuring-rules)。

```js
{
  "rules":{
    "camelcase": 1,
    "comma-dangle": 2,
    "quotes": 0
  }
}
```

#### options.globals

类型：`Array`

指定要声明的全局变量。

```js
{
  "globals":[
    "jQuery",
    "$"
  ]
}
```

#### options.fix

类型：`Boolean`

这个选项指示 ESLint 尝试修复尽可能多的问题。修复程序应用于 gulp stream。修复后内容可以使用 `gulp.dest`保存到文件中 \(参见 [example/fix.js](https://github.com/adametry/gulp-eslint/blob/master/example/fix.js)\)。可以在 ESLint 的[规则列表](https://eslint.org/docs/rules/)中找到可修复的规则。

当应用修复时，被修复文件的 ESLint 结果上的 `fixed` 属性将被设置为 `true`。

#### options.quiet

类型：`Boolean`

当为`true` 时，此选项将从 ESLint 结果中筛选警告消息。这模仿 ESLint CLI [quiet 选项](https://eslint.org/docs/user-guide/command-line-interface#quiet).

类型：`function (message, index, list) { return Boolean(); }`

当提供一个函数时，它将用于过滤 ESLint 结果消息，删除任何不返回 `true` 值\(或不返回值\)的消息。

#### options.envs

类型：`Array`

指定要应用的[环境](https://eslint.org/docs/user-guide/configuring#specifying-environments)列表。

#### options.rulePaths

类型：`Array`

此选项允许您指定用于加载规则文件的其他目录。当您有不适合与 ESLint 绑定的自定义规则时，这是非常有用的。这个选项的工作原理很像 ESLint CLI 的 [rulesdir ](https://eslint.org/docs/user-guide/command-line-interface#rulesdir) 选项。

#### options.configFile

类型：`String`

ESLint 规则配置文件的路径。有关更多信息，请参见 ESLint CLI [config 选项](https://eslint.org/docs/user-guide/command-line-interface#c-config)和[使用配置文件](https://eslint.org/docs/user-guide/configuring#using-configuration-files)。

#### options.warnFileIgnored

类型：`Boolean`

当为 `true` 时，当 ESLint 忽略文件时添加结果警告。这可以用来对由 `gulp.src`加载的不必要的文件进行归档。例如，由于 ESLint 自动忽略 “node\_modules” 文件路径，然而 `gulp.src` 没有这样做，仅仅从 “node\_modules” 目录读取文件就可能要多花几秒钟的时间。

#### options.useEslintrc

类型：`Boolean`

当为`false` 时，ESLint 将不加载 [.eslintrc 文件](https://eslint.org/docs/user-guide/configuring#using-configuration-files)。

### eslint\(configFilePath\)

类型：`String`

定义 `options.configFile` 的简写。

### eslint.result\(action\)

类型：`function (result) {}`

Call a function for each ESLint file result. No returned value is expected. If an error is thrown, it will be wrapped in a Gulp PluginError and emitted from the stream.

```js
gulp.src(['**/*.js','!node_modules/**'])
  .pipe(eslint())
  .pipe(eslint.result(result => {
      // Called for each ESLint result.
      console.log(`ESLint result: ${result.filePath}`);
      console.log(`# Messages: ${result.messages.length}`);
      console.log(`# Warnings: ${result.warningCount}`);
      console.log(`# Errors: ${result.errorCount}`);
  }));
```

类型：`function (result, callback) { callback(error); }`

Call an asynchronous function for each ESLint file result. The callback must be called for the stream to finish. If a value is passed to the callback, it will be wrapped in a Gulp PluginError and emitted from the stream.

### eslint.results\(action\)

类型：`function (results) {}`

Call a function once for all ESLint file results before a stream finishes. No returned value is expected. If an error is thrown, it will be wrapped in a Gulp PluginError and emitted from the stream.

The results list has a "warningCount" property that is the sum of warnings in all results; likewise, an "errorCount" property is set to the sum of errors in all results.

```js
gulp.src(['**/*.js','!node_modules/**'])
  .pipe(eslint())
  .pipe(eslint.results(results => {
      // Called once for all ESLint results.
      console.log(`Total Results: ${results.length}`);
      console.log(`Total Warnings: ${results.warningCount}`);
      console.log(`Total Errors: ${results.errorCount}`);
  }));
```

类型：`function (results, callback) { callback(error); }`

Call an asynchronous function once for all ESLint file results before a stream finishes. The callback must be called for the stream to finish. If a value is passed to the callback, it will be wrapped in a Gulp PluginError and emitted from the stream.

### eslint.failOnError\(\)

Stop a task/stream if an ESLint error has been reported for any file.

```js
// Cause the stream to stop(/fail) before copying an invalid JS file to the output directory
gulp.src(['**/*.js','!node_modules/**'])
  .pipe(eslint())
  .pipe(eslint.failOnError());
```

### eslint.failAfterError\(\)

Stop a task/stream if an ESLint error has been reported for any file, but wait for all of them to be processed first.

```js
// Cause the stream to stop(/fail) when the stream ends if any ESLint error(s) occurred.
gulp.src(['**/*.js','!node_modules/**'])
  .pipe(eslint())
  .pipe(eslint.failAfterError());
```

### eslint.format\(formatter, output\)

Format all linted files once. This should be used in the stream after piping through`eslint`; otherwise, this will find no ESLint results to format.

The`formatter`argument may be a`String`,`Function`, or`undefined`. As a`String`, a formatter module by that name or path will be resolved as a module, relative to`process.cwd()`, or as one of the[ESLint-provided formatters](https://github.com/eslint/eslint/tree/master/lib/formatters). If`undefined`, the ESLint “stylish” formatter will be resolved. A`Function`will be called with an`Array`of file linting results to format.

```js
// use the default "stylish" ESLint formatter
eslint.format()

// use the "checkstyle" ESLint formatter
eslint.format('checkstyle')

// use the "eslint-path-formatter" module formatter
// (@see https://github.com/Bartvds/eslint-path-formatter)
eslint.format('node_modules/eslint-path-formatter')
```

The`output`argument may be a`WritableStream`,`Function`, or`undefined`. As a`WritableStream`, the formatter results will be written to the stream. If`undefined`, the formatter results will be written to[gulp’s log](https://github.com/gulpjs/gulp-util#logmsg). A`Function`will be called with the formatter results as the only parameter.

```js
// write to gulp's log (default)
eslint.format();

// write messages to stdout
eslint.format('junit', process.stdout)
```

### eslint.formatEach\(formatter, output\)

将每个 linted 文件单独格式化。这应该在管道通过 eslint 后用于流体中；否则，将找不到要格式化的 ESLint 结果。

`formatEach` 的参数与 `format` 的参数相同。

## 配置

ESLint 可以通过使用以下插件选项中的任何一个来明确配置：`config`、`rules`、`globals`、或 `env`。如果 [useEslintrc ](https://github.com/adametry/gulp-eslint#useEslintrc)选项没有设置为 `false`，ESLint 将尝试在与要链接的文件相同的目录中解析名为 `.eslintrc `的文件。如果没有找到，将搜索父目录，直到找到 `.eslintrc `或到达根目录为止。

## 忽略文件

ESLint 将忽略在 linting 时没有 `.js` 文件扩展名的文件\([一些插件](https://github.com/gulp-community/gulp-coffee)可能会在中途更改文件扩展名\)。这避免了非 javascript 文件的非故意 linting。

ESLint 还将在 cwd 或父目录中检测 `.eslintignore`。参见 [ESLint ](https://eslint.org/docs/user-guide/configuring#ignoring-files-and-directories) 文档了解如何构造此文件。

## 扩展

ESLint 结果作为 “eslint” 属性附加到经过 Gulp.js 流管道的 vinyl 文件上。这对于遵循初始 eslint 流的流是可用的。 [eslint.result](https://github.com/adametry/gulp-eslint#result) 和 [eslint.results](https://github.com/adametry/gulp-eslint#results) 方法支持扩展和自定义 ESLint 结果处理。

#### Gulp-Eslint 扩展:

* [gulp-eslint-if-fixed](https://github.com/lukeapage/gulp-eslint-if-fixed)
* [gulp-eslint-threshold](https://github.com/krmbkt/gulp-eslint-threshold)



