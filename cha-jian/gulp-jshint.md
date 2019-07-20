## 简介

Gulp 的 jshint 插件，用于对 javascript 文件进行格式检查。

[github 地址](https://github.com/spalger/gulp-jshint)

## 安装

```
npm install --save-dev jshint gulp-jshint
```

## 用法

```js
const jshint = require('gulp-jshint');
const gulp   = require('gulp');

function lintTask()  {
  return gulp.src('./lib/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter('YOUR_REPORTER_HERE'));
}
```

更多请参考 [github 地址](#)



