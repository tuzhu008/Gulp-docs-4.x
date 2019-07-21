## 简介

Gulp 插件，用于 javascript、样式表和 webComponent 注入。

[github 地址](https://github.com/klei/gulp-inject)

## 安装

```
$ npm install --save-dev gulp-inject
```

## 用法

目标文件** `src/index.html`:**

每对注释都是注入占位符\(即. 标签，参见[`options.starttag`](https://github.com/klei/gulp-inject#optionsstarttag) 和 [`options.endtag`](https://github.com/klei/gulp-inject#optionsendtag) \)。

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- inject:css -->
  <!-- endinject -->
</head>
<body>

  <!-- inject:js -->
  <!-- endinject -->
</body>
</html>
```

**`gulpfile.js` 文件：**

```js
var gulp = require('gulp');
var inject = require('gulp-inject');

function indexTask() {
  var target = gulp.src('./src/index.html');
  // 没有必要读取文件(会加快速度)，我们只在乎他们的路径:
  var sources = gulp.src(['./src/**/*.js', './src/**/*.css'], {read: false});

  return target.pipe(inject(sources))
    .pipe(gulp.dest('./src'));
}
```

运行任务后：

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- inject:css -->
  <link rel="stylesheet" href="/src/style1.css">
  <link rel="stylesheet" href="/src/style2.css">
  <!-- endinject -->
</head>
<body>

  <!-- inject:js -->
  <script src="/src/lib1.js"></script>
  <script src="/src/lib2.js"></script>
  <!-- endinject -->
</body>
</html>
```



