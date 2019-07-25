## CSS

```js
var gulp = require('gulp');
var sass = require('gulp-sass');
var concat = require('gulp-concat'); // 合并文件
var autoprefixer = require('gulp-autoprefixer');
var cached = require('gulp-cached'); // 缓存当前任务中的文件，只让已修改的文件通过管道
var concat = require('gulp-concat'); // 合并文件
var rename = require('gulp-rename'); // 重命名

// css （拷贝 *.min.css，常规 CSS 则输出压缩与未压缩两个版本）
function cssTask() {
  return gulp.src('src/css/**/*.css')
    .pipe(concat('main.css')) // 合并 css
    .pipe(cached('css'))
    .pipe(gulp.dest('dist/css')) // 把管道里的所有文件输出到 dist/css 目录
    .pipe(filter(['**/*', '!**/*.min.css'])) // 筛选出管道中的非 *.min.css 文件
    .pipe(autoprefixer('last 6 version'))
    .pipe(gulp.dest('dist/css')) // 把处理过的 css 输出到 dist/css 目录
    .pipe(rename({suffix: '.min'}))
    .pipe(cleanCSS())
    .pipe(gulp.dest('dist/css'))
}
```



