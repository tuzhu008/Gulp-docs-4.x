## 简介

Gulp 插件，用于解析 HTML 文件中的构建块，以替换对非优化脚本或样式表的引用。。

[github 地址](https://github.com/jonkemp/gulp-useref)

## 安装

```
npm install --save-dev gulp-useref
```

## 用法

下面的示例将解析 HTML 中的构建块，替换它们并传递这些文件。构建块中的资源也将被连接起来并在流中传递。

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

如果希望压缩资源或执行其他一些修改，可以使用[ gulp-if ](/cha-jian/gulp-if.md)有条件地处理特定类型的资源。

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

- **type**: either `js`, `css` or `remove`; `remove` will remove the build block entirely without generating a file
- **alternate search path**: (optional) By default the input files are relative to the treated file. Alternate search path allows one to change that. The path can also contain a sequence of paths processed from right to left, using JSON brace array notation e.g `<!-- build:js({path1,path2}) js/lib.js -->`.
- **path**: the file path of the optimized file, the target output
- **parameters**: extra parameters that should be added to the tag

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

## API

### useref(options [, transformStream1 [, transformStream2 [, ... ]]])

Returns a stream with the asset replaced resulting HTML files as well as the concatenated asset files from the build blocks inside the HTML. Supports all options from [useref](https://github.com/jonkemp/useref).

### Transform Streams

Type: `Stream`  
Default: `none`

Transform assets before concat. For example, to integrate source maps:

```js
var gulp = require('gulp'),
    sourcemaps = require('gulp-sourcemaps'),
    useref = require('gulp-useref'),
    lazypipe = require('lazypipe');

gulp.task('default', function () {
    return gulp.src('index.html')
        .pipe(useref({}, lazypipe().pipe(sourcemaps.init, { loadMaps: true })))
        .pipe(sourcemaps.write('maps'))
        .pipe(gulp.dest('dist'));
});
```

### Options

#### options.searchPath

Type: `String` or `Array`  
Default: `none`  

Specify the location to search for asset files, relative to the current working directory. Can be a string or array of strings.

#### options.base

Type: `String`  
Default: `process.cwd()`  

Specify the output folder relative to the cwd.

#### options.noAssets

Type: `Boolean`  
Default: `false`  

Skip assets and only process the HTML files.

#### options.noconcat

Type: `Boolean`  
Default: `false`  

Skip concatenation and add all assets to the stream instead.

#### options.newLine

Type: `String`  
Default: `none`

Add a string that should separate the concatenated files.

#### options.additionalStreams

Type: `Array<Stream>`  
Default: `none`

Use additional streams as sources of assets. Useful for combining gulp-useref with preprocessing tools. For example, to use with TypeScript:

```javascript
var ts = require('gulp-typescript');

// create stream of virtual files
var tsStream = gulp.src('src/**/*.ts')
        .pipe(ts());

gulp.task('default', function () {
    // use gulp-useref normally
    return gulp.src('src/index.html')
        .pipe(useref({ additionalStreams: [tsStream] }))
        .pipe(gulp.dest('dist'));
});
```

#### options.transformPath

Type: `Function`  
Default: `none`

Add a transformPath function in case the path needs to be modified before search happens.

```js
var gulp = require('gulp'),
    useref = require('gulp-useref');

gulp.task('default', function () {
    return gulp.src('app/*.html')
        .pipe(useref({
            transformPath: function(filePath) {
                return filePath.replace('/rootpath','')
            }
        }))
        .pipe(gulp.dest('dist'));
});
```
