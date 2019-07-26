## image

```js
var gulp = require('gulp');
var cached = require('gulp-cached'); // 缓存当前任务中的文件，只让已修改的文件通过管道
var imagemin = require('gulp-imagemin');// 图片优化

// image
function imageTask() {
  return gulp.src('src/img/**/*.{jpg,jpeg,png,gif}')
    .pipe(cached('image'))
    .pipe(imagemin({optimizationLevel: 3, progressive: true, interlaced: true, multipass: true}))
    // 取值范围：0-7（优化等级）,是否无损压缩jpg图片，是否隔行扫描gif进行渲染，是否多次优化svg直到完全优化
    .pipe(gulp.dest('dist/img'))
}
```

压缩 jpeg、png 等的时候可能需要添加插件：

```js
// image
function imageTask() {
  return gulp.src('src/img/**/*.{jpg,jpeg,png,gif}')
    .pipe(cached('image'))
    .pipe([
      imagemin.jpegtran({progressive: true}) // 插件
    ], imagemin({optimizationLevel: 3, progressive: true, interlaced: true, multipass: true}))
    .pipe(gulp.dest('dist/img'))
}
```



