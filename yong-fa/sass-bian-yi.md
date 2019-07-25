## Sass

```js
var gulp = require('gulp');
var sass = require('gulp-sass');
var autoprefixer = require('gulp-autoprefixer');
var cleanCSS = require('gulp-clean-css');
var rename = require('gulp-rename'); // 重命名

sass.compiler = require('node-sass');


// sass
function sassTask() {
  return gulp.src('src/sass/**/*.scss')  // 传入 sass 目录及子目录下的所有 .scss 文件生成文件流通过管道并设置输出格式
    .pipe(cached('sass'))  // 缓存传入文件，只让已修改的文件通过管道（第一次执行是全部通过，因为还没有记录缓存）
    .pipe(sass())
    .pipe(autoprefixer('last 6 version')) // 添加 CSS 浏览器前缀，兼容最新的5个版本
    .pipe(gulp.dest('dist/css')) // 输出到 dist/css 目录下（不影响此时管道里的文件流）
    .pipe(rename({suffix: '.min'})) // 对管道里的文件流添加 .min 的重命名
    .pipe(cleanCSS()) // 压缩 CSS
    .pipe(gulp.dest('dist/css')) // 输出到 dist/css 目录下，此时每个文件都有压缩（*.min.css）和未压缩(*.css)两个版本
}
```



