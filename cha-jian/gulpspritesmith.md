## 简介

Gulp 插件，用于将一组图像转换为雪碧图和 CSS 变量。

[github 地址](https://github.com/twolfson/gulp.spritesmith)

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

更多请参考 [github](#)

