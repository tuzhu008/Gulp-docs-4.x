## 简介

Gulp 的 CSS 插件，用于为 CSS 添加供应商前缀，内部使用 [Autoprefixer](https://github.com/postcss/autoprefixer)。

[github 地址](https://github.com/sindresorhus/gulp-autoprefixer)

## 安装

```
npm install node-sass gulp-autoprefixer
```

## 用法

```js
const gulp = require('gulp');
const autoprefixer = require('gulp-autoprefixer');

function defaultTask {
  return gulp.src('src/app.css')
    .pipe(autoprefixer({
      browsers: ['last 2 versions'],
      cascade: false
    }))
    .pipe(gulp.dest('dist'))
}
```

## API

### autoprefixer\(\[options\]\)

#### options

类型：`object`

请参见 [options](https://github.com/postcss/autoprefixer#options)。

## Source Maps

可以使用 [gulp-sourcemaps ](/cha-jian/gulp-sourcemaps.md)生成源映射：

```js
const gulp = require('gulp');
const sourcemaps = require('gulp-sourcemaps');
const autoprefixer = require('gulp-autoprefixer');
const concat = require('gulp-concat');

function defaultTask() {
  return gulp.src('src/**/*.css')
    .pipe(sourcemaps.init())
    .pipe(autoprefixer())
    .pipe(concat('all.css'))
    .pipe(sourcemaps.write('.'))
    .pipe(gulp.dest('dist'))
}
```



