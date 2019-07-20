## 简介

Gulp 插件，用于压缩 HTML，内部使用 [**html-minifier**](https://github.com/kangax/html-minifier)。

[github 地址](https://github.com/jonschlinkert/gulp-htmlmin)

## 安装

```
npm install --save-dev gulp-htmlmin
```

## 用法

```js
const gulp = require('gulp');
const htmlmin = require('gulp-htmlmin');

function minifyHTML() {
  return gulp.src('src/*.html')
    .pipe(htmlmin({ collapseWhitespace: true }))
    .pipe(gulp.dest('dist'));
}

```



