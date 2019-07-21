## 简介

Gulp 的 [Sass](https://www.sass.hk/) 插件，用于 livereload，与  [livereload chrome 扩展](http://livereload.com/extensions/)或者 [livereload 中间件](https://github.com/intesso/connect-livereload)搭配使用.。

[github 地址](https://github.com/vohof/gulp-livereload)

## 安装

```
npm install --save-dev gulp-livereload
```

## 用法

```javascript
var gulp = require('gulp'),
    less = require('gulp-less'),
    livereload = require('gulp-livereload');

gulp.task('less', function() {
  gulp.src('less/*.less')
    .pipe(less())
    .pipe(gulp.dest('css'))
    .pipe(livereload());
});

gulp.task('watch', function() {
  livereload.listen();
  gulp.watch('less/*.less', ['less']);
});
```

**See [examples](examples)**.

## API & Variables

### Options (Optional)

These options can either be set through `livereload.listen(options)` or `livereload(options)`.

```
port                     Server port
host                     Server host
basePath                 Path to prepend all given paths
start                    Automatically start
quiet        false       Disable console logging
reloadPage   index.html  Path to the browser's current page for a full page reload
```

### livereload([options])

Creates a stream which notifies the livereload server on what changed.

### livereload.listen([options])

Starts a livereload server. It takes an optional options parameter that is the same as the one noted above. Also you dont need to worry with multiple instances as this function will end immediately if the server is already runing.

### livereload.changed(path)

Alternatively, you can call this function to send changes to the livereload server. You should provide either a simple string or an object, if an object is given it expects the object to have a `path` property.

> NOTE: Calling this function without providing a `path` will do nothing.

### livereload.reload([file])

You can also tell the browser to refresh the entire page. This assumes the page is called `index.html`, you can change it by providing an **optional** `file` path or change it globally with the options `reloadPage`.

###  livereload.middleware

You can also directly access the middleware of the underlying server instance (mini-lr.middleware) for hookup through express, connect, or some other middleware app

### livereload.server

gulp-livereload also reveals the underlying server instance for direct access if needed. The instance is a "mini-lr" instance that this wraps around. If the server is not running then this will be `undefined`.


