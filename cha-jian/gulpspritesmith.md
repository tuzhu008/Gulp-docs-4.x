## 简介

Gulp 插件，用于将一组图像转换为雪碧图和 CSS 变量。

[github 地址](https://github.com/twolfson/gulp.spritesmith)

## 安装

```
npm install --save-dev gulp.spritesmith
```

## 用法

```js
var gulp = require('gulp');
var spritesmith = require('gulp.spritesmith');

function spriteTask() {
  return gulp.src('images/*.png')
    .pipe(spritesmith({
      imgName: 'sprite.png',
      cssName: 'sprite.css'
    }))
    .pipe(gulp.dest('path/to/output/'));
}
```



