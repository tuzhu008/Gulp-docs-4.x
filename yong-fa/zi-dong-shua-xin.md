## 自动刷新

思路：

1. 监听文件变化，执行各文件相关的任务
2. 由于 css 需要编译压缩再输出，特殊处理
3. 文件变化时，刷洗页面

```js
var gulp = require('gulp');
var cached = require('gulp-cached');
var browserSync = require('browser-sync');// 保存自动刷新

// styleReload （结合 watch 任务，无刷新CSS注入）
const styleReload = (parallel(sassTask, cssTask), function () {
  return gulp.src(['dist/css/**/*.css'])
    .pipe(cached('style'))
    .pipe(browserSync.reload({stream: true})); // 使用无刷新 browserSync 注入 CSS
});

// watch 开启本地服务器并监听
function watchTask() {
  browserSync.init({
    server: {
      baseDir: 'dist' // 在 dist 目录下启动本地服务器环境，自动启动默认浏览器
    }
  });

  // 监控 SASS 文件，有变动则执行CSS注入
  gulp.watch('src/sass/**/*.scss', styleReload);

  // 监控 CSS 文件，有变动则执行CSS注入
  gulp.watch('src/css/**/*.css', styleReload);

  // 监控 js 文件，有变动则执行 script 任务
  gulp.watch('src/js/**/*.js', scriptTask);

  // 监控图片文件，有变动则执行 image 任务
  gulp.watch('src/img/**/*', imageTask);

  // 监控 html 文件，有变动则执行 html 任务
  gulp.watch('src/**/*.html', htmlTask);

  // 监控 dist 目录下除 css 目录以外的变动（如js，图片等），则自动刷新页面
  gulp.watch(['dist/**/*', '!dist/css/**/*']).on('change', browserSync.reload);
}
```



