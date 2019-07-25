## JS

```js
var gulp = require('gulp');
var uglify = require('gulp-uglify'); // js 压缩
var concat = require('gulp-concat'); // 合并文件
var cached = require('gulp-cached'); // 缓存当前任务中的文件，只让已修改的文件通过管道
var rename = require('gulp-rename'); // 重命名

// script （拷贝 *.min.js，常规 js 则输出压缩与未压缩两个版本）
function scriptTask() {
  return gulp.src(['src/js/**/*.js'])
    .pipe(concat('main.js')) // 合并 js
    .pipe(cached('script'))
    .pipe(gulp.dest('dist/js'))
    .pipe(filter(['**/*', '!**/*.min.js'])) // 筛选出管道中的非 *.min.js 文件
    // .pipe(jshint('.jshintrc')) // js的校验与合并，根据需要开启
    // .pipe(jshint.reporter('default'))
    // .pipe(concat('main.js'))
    // .pipe(gulp.dest('dist/js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('dist/js'))
}
```



