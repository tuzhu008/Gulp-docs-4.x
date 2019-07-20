## 简介

Gulp 的 CSS 压缩插件，内部使用 [clean-css](https://github.com/jakubpawlowicz/clean-css)。

[github 地址](https://github.com/scniro/gulp-clean-css#readme)

## 安装

```
npm install node-sass gulp-clean-css
```

## API

### cleanCSS\(\[_options_\], \[_callback_\]\)

#### options

参见 [`CleanCSS options`](https://github.com/jakubpawlowicz/clean-css#how-to-use-clean-css-api) 。

```js
let gulp = require('gulp');
let cleanCSS = require('gulp-clean-css');

function minifyCSS() {
  return gulp.src('styles/*.css')
    .pipe(cleanCSS({compatibility: 'ie8'}))
    .pipe(gulp.dest('dist'));
}
```

#### callback

用于从底层的  [`minify()`](https://github.com/jakubpawlowicz/clean-css#using-api) 调用返回详细信息。一个示例用例可以包括缩小文件的日志 `stats`。除了默认对象之外，`gulp-clean-css` 还提供了用于进一步分析的文件 `name` 和 `path`。

```js
let gulp = require('gulp');
let cleanCSS = require('gulp-clean-css');

function minifyCSS() {
  return gulp.src('styles/*.css')
    .pipe(cleanCSS({debug: true}, (details) => {
      console.log(`${details.name}: ${details.stats.originalSize}`);
      console.log(`${details.name}: ${details.stats.minifiedSize}`);
    }))
  .pipe(gulp.dest('dist'));
}
```

可以使用 [gulp-sourcemaps ](/cha-jian/gulp-sourcemaps.md)生成源映射：

```js
let gulp = require('gulp');
let cleanCSS = require('gulp-clean-css');
let sourcemaps = require('gulp-sourcemaps');

function minifyCSS() {
  return gulp.src('./src/*.css')
    .pipe(sourcemaps.init())
    .pipe(cleanCSS())
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('dist'));
}
```



