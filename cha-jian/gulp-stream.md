## 简介

Gulp 插件，通过将内容哈希附加到文件名来对静态资产进行修正：`unicorn.css` → `unicorn-d41d8cd98f.css`

[github 地址](https://github.com/sindresorhus/gulp-rev)

## 安装

```
npm install --save-dev gulp-rev
```

## 用法

```js
const gulp = require('gulp');
const rev = require('gulp-rev');

function defaultTask() {
  return gulp.src('src/*.css')
    .pipe(rev())
    .pipe(gulp.dest('dist'))
}
```



