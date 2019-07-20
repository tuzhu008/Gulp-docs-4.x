## 简介

Gulp 的插件，用于删除指定的文件或者文件夹。

[github 地址](https://github.com/peter-vilja/gulp-clean)

## 安装

```
npm install --save-dev gulp-clean
```

## 用法

```js
var gulp = require('gulp');
var clean = require('gulp-clean');

function defaultTask() {
  return gulp.src('app/tmp', {read: false})
    .pipe(clean());
}
```

`read` 选项 为 `false` ，会阻止 gulp 读取文件的内容，这使任务执行得更快。如果在同一流中清理后需要文件及其内容，请不要将`read` 选项设置为 `false`。

```js
var gulp = require('gulp');
var clean = require('gulp-clean');

function defaultTask() {
  return gulp.src('app/tmp/index.js')
    .pipe(clean({force: true}))
    .pipe(gulp.dest('dist'));
};
```

**为了安全，对于当前工作目录之外的文件和文件夹，只能在将选项 **`force`** 设置为 **`true`** 时删除。**

作为依赖使用：

```js
var gulp = require('gulp');
var clean = require('gulp-clean');

function cleanScript() {
  return gulp.src('app/tmp/*.js', {read: false})
    .pipe(clean());
}

const scripts = series(cleanScript, function () {
  return gulp.src('app/scripts/*.js')
    .pipe(gulp.dest('app/tmp'));
});

const default = series(scripts);
```



