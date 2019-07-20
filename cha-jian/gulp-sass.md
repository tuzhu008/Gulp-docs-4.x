## 简介

Gulp 的 [Sass](https://www.sass.hk/) 插件，用于解析 `.sass` 文件。

[github 地址](https://github.com/dlmanning/gulp-sass)

## 安装

```
npm install node-sass gulp-sass --save-dev
```

## 用法

```js
'use strict';

var gulp = require('gulp');
var sass = require('gulp-sass');

sass.compiler = require('node-sass');

function sassTask() {
  return gulp.src('./sass/**/*.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest('./css'));
};

function watchSass() {
  gulp.watch('./sass/**/*.scss', ['sass']);
};
```

也可以同步编译，如下所示:

```js
'use strict';

var gulp = require('gulp');
var sass = require('gulp-sass');

sass.compiler = require('node-sass');

function sassTask() {
  return gulp.src('./sass/**/*.scss')
    .pipe(sass.sync().on('error', sass.logError))
    .pipe(gulp.dest('./css'));
};

function watchSass() {
  gulp.watch('./sass/**/*.scss', ['sass']);
};
```

通过设置 `sass.compiler` 属性，可以选择使用 [Dart Sass](http://sass-lang.com/dart-sass) 还是 [Node Sass](https://github.com/sass/node-sass)。默认情况下将使用 Node Sass，但强烈建议您显式地将其设置为向前兼容，以防默认值发生更改。

注意，在使用 Dart Sass 时，由于异步回调的开销，默认情况下同步编译的速度是异步编译的两倍。为了避免这种开销，可以使用  [`fibers`](https://www.npmjs.com/package/fibers) 包从同步代码路径调用异步导入器。要启用此功能，请将 `Fiber` 类传递给 `fiber` 选项:

```js
'use strict';

var Fiber = require('fibers');
var gulp = require('gulp');
var sass = require('gulp-sass');

sass.compiler = require('sass');

function sassTask () {
  return gulp.src('./sass/**/*.scss')
    .pipe(sass({fiber: Fiber}).on('error', sass.logError))
    .pipe(gulp.dest('./css'));
};

function watchSass() {
  gulp.watch('./sass/**/*.scss', ['sass']);
};
```

## 选项

像传递给 [Node Sass](https://github.com/sass/node-sass#options) 选项 一样传递选项；它们将像在使用 Node Sass 一样被传递。除了 gulp-sass 内部使用的 `data` 选项。使用 `file` 选项也是不受支持的，并且会导致未知的行为，这些行为可能在没有通知的情况下发生更改。

例如：

```js
function sassTask() {
 return gulp.src('./sass/**/*.scss')
   .pipe(sass({outputStyle: 'compressed'}).on('error', sass.logError))
   .pipe(gulp.dest('./css'));
};
```

或同步代码：

```js
function sassTask() {
 return gulp.src('./sass/**/*.scss')
   .pipe(sass.sync({outputStyle: 'compressed'}).on('error', sass.logError))
   .pipe(gulp.dest('./css'));
};
```

## Source Maps

`gulp-sass` 可以与 [gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md) 一起使用，用以为文件生成源映射。您将需要在运行 `gulp-sass` 之前初始化 [gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md)，然后编写源代码映射，如下所示:

```js
var sourcemaps = require('gulp-sourcemaps');

function sassTask() {
 return gulp.src('./sass/**/*.scss')
  .pipe(sourcemaps.init())
  .pipe(sass().on('error', sass.logError))
  .pipe(sourcemaps.write())
  .pipe(gulp.dest('./css'));
};
```

默认情况下，[gulp-sourcemaps](/cha-jian/gulp-sourcemaps.md) 将源映射内联到已编译的 CSS 文件中。要将它们写入单独的文件，请在 `sourcemaps.write()` 函数中指定一个相对文件路径，如下所示:

```js
var sourcemaps = require('gulp-sourcemaps');

function lessTask() {
  return gulp.src('./less/**/*.less')
    .pipe(sourcemaps.init())
    .pipe(less())
    .pipe(sourcemaps.write('./maps'))
    .pipe(gulp.dest('./public/css'));
}
```

## 错误处理

默认情况下，当发生错误时，gulp 任务将失败，所有流将停止。要更改此行为，请查看[此处](https://github.com/gulpjs/gulp/blob/master/docs/recipes/combining-streams-to-handle-errors.md)的错误处理文档。

