## 简介

基于 [preprocess](https://github.com/jsoverson/preprocess#directive-syntax) 的 gulp 插件，用于根据上下文对内容进行预处理。

[github 地址](https://github.com/pioug/gulp-preprocess)

## 安装

```
npm install --save-dev gulp-preprocess
```

## 用法

### Gulpfile

```js
var preprocess = require("gulp-preprocess");

gulp.task("html", function() {
  gulp
    .src("./app/*.html")
    .pipe(preprocess({ context: { NODE_ENV: "production", DEBUG: true } })) // To set environment variables in-line
    .pipe(gulp.dest("./dist/"));
});

gulp.task("scripts", function() {
  gulp
    .src(["./app/*.js"])
    .pipe(preprocess())
    .pipe(gulp.dest("./dist/"));
});
```

### 示例 HTML 文件

```html
<head>
  <title>Your App

  <!-- @if NODE_ENV='production' -->
  <script src="some/production/lib/like/analytics.js"></script>
  <!-- @endif -->

</head>
<body>
  <!-- @ifdef DEBUG -->
  <h1>Debugging mode - <!-- @echo RELEASE_TAG --> </h1>
  <!-- @endif -->
  <p>
  <!-- @include welcome_message.txt -->
  </p>
</body>
```

### 示例 JavaScript 文件

```js
var configValue = "/* @echo FOO */" || "default value";

// @if NODE_ENV='production'
const name = 'AAA';
// @endif

// @ifdef DEBUG
someDebuggingCall();
// @endif
```

### 更多示例

更多示例请参考 preprocess 的 [README](https://github.com/jsoverson/preprocess#directive-syntax)。

## API

### preprocess\(options\)

#### options.context

类型：`Object`

默认值：`{}`

Context for directives used in your preprocessed files. The default context consists of the current user environment variables. Custom context is merged with`process.env`.

#### options.includeBase

类型：`String`

Base directory for included files. By default, the path to included files is relative to the file currently being processed.

#### options.extension

类型：`String`

Override the file extension. This determines what[regular expressions are used for comments](https://github.com/jsoverson/preprocess/blob/master/lib/regexrules.js). You may wish to do this if you are using a custom extension or need to force a particular comment syntax \(for example, to allow HTML-style comments in`.php`files\).



