## 去缓存

1. 生成  mainfest
2. 根据 mainfest 对 html 进行替换

```js
// sass
function sassTask() {
  return gulp.src('src/scss/**/*.scss')
    .pipe(rev()) // 位置很重重要
    .pipe(sass())
    .pipe(gulp.dest('dist/css')) 
    .pipe(rename({suffix: '.min'}))
    .pipe(cleanCSS())
    .pipe(gulp.dest('dist/css'))
    .pipe(rev.manifest()) // 生成 mainfest
    .pipe(rename({suffix: '.scss'}))
    .pipe(gulp.dest('dist/rev/'))
}

// css
function cssTask() {
  return gulp.src('src/css/**/*.css')
    .pipe(gulp.dest('dist/css'))
    .pipe(rev()) // 位置很重重要
    .pipe(rename({suffix: '.min'}))
    .pipe(cleanCSS())
    .pipe(gulp.dest('dist/css'))
    .pipe(rev.manifest()) // 生成 mainfest
    .pipe(rename({suffix: '.css'}))
    .pipe(gulp.dest('dist/rev/'))
}

```



