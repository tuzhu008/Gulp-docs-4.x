## 简介

Gulp 插件，用于解析 HTML 文件中的构建块，以替换对非优化脚本或样式表的引用。。

[github 地址](https://github.com/jonkemp/gulp-useref)

## 安装

```
npm install --save-dev gulp-useref
```

## 用法

下面的示例将解析 HTML 中的构建块，替换它们并传递这些文件。构建块中的资产也将被连接起来并在流中传递。

```js
var gulp = require('gulp');
var useref = require('gulp-useref');

function defaultTask() {
  return gulp.src('app/*.html')
    .pipe(useref())
    .pipe(gulp.dest('dist'));
}
```

使用选项：

```js
var gulp = require('gulp');
var useref = require('gulp-useref');

function defaultTask() {
  return gulp.src('app/*.html')
    .pipe(useref({ searchPath: '.tmp' }))
    .pipe(gulp.dest('dist'));
}
```

如果希望缩小资产规模或执行其他一些修改，可以使用 gulp-if 有条件地处理特定类型的资产。

```js
var gulp = require('gulp');
var useref = require('gulp-useref');
var gulpif = require('gulp-if');
var uglify = require('gulp-uglify');
var minifyCss = require('gulp-clean-css');

function htmlTask() {
  return gulp.src('app/*.html')
    .pipe(useref())
    .pipe(gulpif('*.js', uglify()))
    .pipe(gulpif('*.css', minifyCss()))
    .pipe(gulp.dest('dist'));
}
```

块表示为:

```html
<!-- build:<type>(alternate search path) <path> <parameters> -->
... HTML Markup, list of script / link tags.
<!-- endbuild -->
```

* type: either js, css or remove; remove will remove the build block entirely without generating a file

* alternate search path: \(optional\) By default the input files are relative to the treated file. Alternate search path allows one to change that. The path can also contain a sequence of paths processed from right to left, using JSON brace array notation e.g &lt;!-- build:js\({path1,path2}\) js/lib.js --&gt;.

* path: the file path of the optimized file, the target output

* parameters: extra parameters that should be added to the tag

填妥的表格如下:

```html
<html>
<head>
    <!-- build:css css/combined.css -->
    <link href="css/one.css" rel="stylesheet">
    <link href="css/two.css" rel="stylesheet">
    <!-- endbuild -->
</head>
<body>
    <!-- build:js scripts/combined.js -->
    <script type="text/javascript" src="scripts/one.js"></script>
    <script type="text/javascript" src="scripts/two.js"></script>
    <!-- endbuild -->
</body>
</html>
```

得到的 HTML 将是：

```html
<html>
<head>
    <link rel="stylesheet" href="css/combined.css"/>
</head>
<body>
    <script src="scripts/combined.js"></script>
</body>
</html>
```

参见 [useref](https://github.com/jonkemp/useref) 获取更多信息。

