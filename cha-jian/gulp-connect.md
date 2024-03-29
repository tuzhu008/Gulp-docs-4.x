## 简介

Gulp 插件，用于解析 `.sass` 文件。

[github 地址](https://github.com/avevlad/gulp-connect)

## 安装

```
npm install --save-dev gulp-connect
```

## 用法

```js
var gulp = require('gulp');
var connect = require('gulp-connect');

gulp.task('connect', function() {
  connect.server();
});

gulp.task('default', ['connect']);
```

#### LiveReload

```js
var gulp = require('gulp');
var connect = require('gulp-connect');

gulp.task('connect', function() {
  connect.server({
    root: 'app',
    livereload: true
  });
});

gulp.task('html', function () {
  gulp.src('./app/*.html')
    .pipe(gulp.dest('./app'))
    .pipe(connect.reload());
});

gulp.task('watch', function () {
  gulp.watch(['./app/*.html'], ['html']);
});

gulp.task('default', ['connect', 'watch']);
```

#### 启动和停止服务器

```js
gulp.task('jenkins-tests', function() {
  connect.server({
    port: 8888
  });
  // run some headless tests with phantomjs
  // when process exits:
  connect.serverClose();
});
```

#### 多个服务器

```js
var gulp = require('gulp');
var connect = require('gulp-connect');
var stylus = require('gulp-stylus');

gulp.task('connectDev', function () {
  connect.server({
    name: 'Dev App',
    root: ['app', 'tmp'],
    port: 8000,
    livereload: true
  });
});

gulp.task('connectDist', function () {
  connect.server({
    name: 'Dist App',
    root: 'dist',
    port: 8001,
    livereload: true
  });
});

gulp.task('html', function () {
  gulp.src('./app/*.html')
    .pipe(gulp.dest('./app'))
    .pipe(connect.reload());
});

gulp.task('stylus', function () {
  gulp.src('./app/stylus/*.styl')
    .pipe(stylus())
    .pipe(gulp.dest('./app/css'))
    .pipe(connect.reload());
});

gulp.task('watch', function () {
  gulp.watch(['./app/*.html'], ['html']);
  gulp.watch(['./app/stylus/*.styl'], ['stylus']);
});

gulp.task('default', ['connectDist', 'connectDev', 'watch']);
```

#### 支持 http2

如果安装了 [http2](https://www.npmjs.com/package/http2) 包，并且使用 https 连接进行 gulp 连接，则首选 http2 而不是 http 1。

## API

#### options.root

Type: `Array or String`  
Default: `Directory with gulpfile`

The root path

#### options.port

Type: `Number`  
Default: `8080`

The connect webserver port

#### options.host

Type: `String`  
Default: `localhost`

#### options.name

Type: `String`  
Default: `Server`

The name that will be output when the server starts/stops.

#### options.https

Type: `Object`  
Default: `false`

Can be any options documented at [https://nodejs.org/api/https.html\#https\_https\_createserver\_options\_requestlistener](https://nodejs.org/api/https.html#https_https_createserver_options_requestlistener)

When https is just set to `true` \(boolean\), then internally some defaults will be used.

#### options.livereload

Type: `Object or Boolean`  
Default: `false`

#### options.livereload.port

Type: `Number`  
Default: `35729`

Overrides the hostname of the script livereload injects in index.html

#### options.livereload.hostname

Type: `String`  
Default: 'undefined'

#### options.fallback

Type: `String`  
Default: `undefined`

Fallback file \(e.g. `index.html`\)

#### options.middleware

Type: `Function`  
Default: `[]`

#### options.debug

Type: `Boolean`  
Default: `false`

#### options.index

Type: `Boolean or String of a new index pass or Array of new indexes in preferred order`  
Default: `true`

```js
gulp.task('connect', function() {
  connect.server({
    root: "app",
    middleware: function(connect, opt) {
      return [
        // ...
      ]
    }
  });
});
```



