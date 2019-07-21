## 简介

Gulp 插件，可以根据文件特性忽略流中的文件。

[github 地址](https://github.com/robrich/gulp-ignore)

## 安装

```
npm install --save-dev gulp-ignore
```

## 用法

1: Exclude things from the stream

**Exclude things from entering the stream**

![][glob]

```javascript
var uglify = require('gulp-uglify');

gulp.task('task', function() {
  gulp.src(['./**/*.js', '!./node_modules/**'])
    .pipe(uglify())
    .pipe(gulp.dest('./dist/'));
});
```

Grab all JavaScript files that aren't in the node_modules folder, uglify them, and write them.
This is fastest because nothing in node_modules ever leaves `gulp.src()`


2: Remove things from the stream

**Remove from here on**

![][exclude]

```javascript
var gulpIgnore = require('gulp-ignore');
var uglify = require('gulp-uglify');
var jshint = require('gulp-jshint');

var condition = './gulpfile.js';

gulp.task('task', function() {
  gulp.src('./**/*.js')
    .pipe(jshint())
    .pipe(gulpIgnore.exclude(condition))
    .pipe(uglify())
    .pipe(gulp.dest('./dist/'));
});
```

Run JSHint on everything, remove gulpfile from the stream, then uglify and write everything else.

3: Filter only matching things

**Include from here on**

![][include]

```javascript
var gulpIgnore = require('gulp-ignore');
var uglify = require('gulp-uglify');
var jshint = require('gulp-jshint');

var condition = './public/**.js';

gulp.task('task', function() {
  gulp.src('./**/*.js')
    .pipe(jshint())
    .pipe(gulpIgnore.include(condition))
    .pipe(uglify())
    .pipe(gulp.dest('./dist/'));
});
```

Run JSHint on everything, filter to include only files from in the public folder, then uglify and write them.


4: Conditionally filter content, include everything down-stream

**Condition**

![][condition]

```javascript
var gulpif = require('gulp-if'); // This is gulp-if, not gulp-ignore
var uglify = require('gulp-uglify');

var condition = function(file) {
  // Only files whose contents match a pattern
  return /a pattern/g.test(String(file.contents));
};

gulp.task('task', function() {
  gulp.src('./src/*.js')
    .pipe(gulpif(condition, uglify()))
    .pipe(gulp.dest('./dist/'));
});
```
Only uglify the content if the condition is true, but send all the files to the dist folder.


## API

### exclude(condition [, minimatchOptions])

Exclude files whose `file.path` matches, include everything else

### include(condition [, minimatchOptions])

Include files whose `file.path` matches, exclude everything else

### condition

Type: `boolean` or [`stat`](http://nodejs.org/api/fs.html#fs_class_fs_stats) object or `function` that takes in a vinyl file and returns a boolean or `RegularExpression` that works on the `file.path`

The condition parameter is any of the conditions supported by [gulp-match](https://github.com/robrich/gulp-match).  The `file.path` is passed into `gulp-match`.

If a function is given, then the function is passed a vinyl `file`. The function should return a `boolean`.

##### minimatchOptions

Optional, if it's a glob condition, these options are passed to [https://github.com/isaacs/minimatch](minimatch).

[condition]: https://rawgithub.com/robrich/gulp-ignore/master/img/condition.svg
[ternary]: https://rawgithub.com/robrich/gulp-ignore/master/img/ternary.svg
[exclude]: https://rawgithub.com/robrich/gulp-ignore/master/img/exclude.svg
[include]: https://rawgithub.com/robrich/gulp-ignore/master/img/include.svg
[glob]: https://rawgithub.com/robrich/gulp-ignore/master/img/glob.svg


