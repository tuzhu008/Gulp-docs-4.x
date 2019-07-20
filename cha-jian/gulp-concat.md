## 简介

Gulp 的插件，用于合并多个流。

[github 地址](https://github.com/gulp-community/gulp-concat)

## 安装

```
npm install node-sass gulp-concat
```

## 用法

```js
var concat = require('gulp-concat');

function scriptsTask() {
  return gulp.src('./lib/*.js')
    .pipe(concat('all.js'))
    .pipe(gulp.dest('./dist/'));
};
```

这将通过操作系统的换行符 concat 文件。它将从通过它的第一个文件获取基础目录。

文件将按照在 `gulp.src` 函数中指定的顺序连接起来。例如，要按照 `./lib/file3.js` 、`./lib/file1.js` 、`./lib/file2.js` 这个顺序链接文件，按照下面的代码将创建一个任务：

```js
var concat = require('gulp-concat');

function scriptsTask() {
  return gulp.src(['./lib/file3.js', './lib/file1.js', './lib/file2.js'])
    .pipe(concat('all.js'))
    .pipe(gulp.dest('./dist/'));
}
```

要更改换行符，只需将一个对象作为第二个参数传递给 `concat`，换行符可以是任意值\(如果希望支持任何操作系统查看它，可以使用 `\r\n`\)。

例如：

```js
.pipe(concat('main.js', {newLine: ';'}))
```

为了指定 `cwd`、`path` 和其他 [Vinyl](https://github.com/wearefractal/vinyl) 属性，`gulp-concat` 接受 `Object` 作为第一个参数：

```js
var concat = require('gulp-concat');

function scriptsTask() {
  return gulp.src(['./lib/file3.js', './lib/file1.js', './lib/file2.js'])
    .pipe(concat({ path: 'new.js', stat: { mode: 0666 }}))
    .pipe(gulp.dest('./dist'));
}
```

连接后的文件输出到 `./dist/new.js` 中。

### Source maps

可以使用 [gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md) 生成源映射：

```js
var gulp = require('gulp');
var concat = require('gulp-concat');
var sourcemaps = require('gulp-sourcemaps');

function scriptsTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init())
      .pipe(concat('all.js'))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('dist'));
}
```



