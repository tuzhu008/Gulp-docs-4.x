## 简介

Gulp 插件，用于有条件地运行任务。

[github 地址](https://github.com/robrich/gulp-if)

## 安装

```
npm install --save-dev gulp-if
```

## 用法

1: 有条件地过滤内容

**条件**

![](https://rawgithub.com/robrich/gulp-if/master/img/condition.svg)

```javascript
var gulpif = require('gulp-if');
var uglify = require('gulp-uglify');

var condition = true; // TODO: add business logic

gulp.task('task', function() {
  gulp.src('./src/*.js')
    .pipe(gulpif(condition, uglify()))
    .pipe(gulp.dest('./dist/'));
});
```

只有条件为 true 时 uglify 内容，所有的文件都被输出到 dist 文件夹

2: 三元过滤

**Ternary**

![](https://rawgithub.com/robrich/gulp-if/master/img/ternary.svg)

```javascript
var gulpif = require('gulp-if');
var uglify = require('gulp-uglify');
var beautify = require('gulp-beautify');

var condition = function (file) {
  // TODO: add business logic
  return true;
}

gulp.task('task', function() {
  gulp.src('./src/*.js')
    .pipe(gulpif(condition, uglify(), beautify()))
    .pipe(gulp.dest('./dist/'));
});
```

If condition returns true, uglify else beautify, then send everything to the dist folder

3: 从流中删除内容

**Remove from here on**

![](https://rawgithub.com/robrich/gulp-if/master/img/exclude.svg)

```javascript
var gulpIgnore = require('gulp-ignore');
var uglify = require('gulp-uglify');
var jshint = require('gulp-jshint');

var condition = './gulpfile.js';

gulp.task('task', function() {
  gulp.src('./*.js')
    .pipe(jshint())
    .pipe(gulpIgnore.exclude(condition))
    .pipe(uglify())
    .pipe(gulp.dest('./dist/'));
});
```

Run JSHint on everything, remove gulpfile from the stream, then uglify and write everything else.

4: 把一些东西从流中排除

**Exclude things from entering the stream**

![](https://rawgithub.com/robrich/gulp-if/master/img/glob.svg)

```javascript
var uglify = require('gulp-uglify');

gulp.task('task', function() {
  gulp.src(['./*.js', '!./node_modules/**'])
    .pipe(uglify())
    .pipe(gulp.dest('./dist/'));
});
```

Grab all JavaScript files that aren't in the node\_modules folder, uglify them, and write them.  
This is fastest because nothing in node\_modules ever leaves `gulp.src()`

## works great with [lazypipe](https://github.com/OverZealous/lazypipe)

Lazypipe creates a function that initializes the pipe chain on use.  This allows you to create a chain of events inside the gulp-if condition.  This scenario will run jshint analysis and reporter only if the linting flag is true.

```js
var gulpif = require('gulp-if');
var jshint = require('gulp-jshint');
var uglify = require('gulp-uglify');
var lazypipe = require('lazypipe');

var linting = false;
var compressing = false;

var jshintChannel = lazypipe()
  // adding a pipeline step
  .pipe(jshint) // notice the stream function has not been called!
  .pipe(jshint.reporter)
  // adding a step with an argument
  .pipe(jshint.reporter, 'fail');

gulp.task('scripts', function () {
  return gulp.src(paths.scripts.src)
    .pipe(gulpif(linting, jshintChannel()))
    .pipe(gulpif(compressing, uglify()))
    .pipe(gulp.dest(paths.scripts.dest));
});
```

[source](https://github.com/spenceralger/gulp-jshint/issues/38#issuecomment-40423932)

## works great inside [lazypipe](https://github.com/OverZealous/lazypipe)

Lazypipe assumes that all function parameters are static, gulp-if arguments need to be instantiated inside each lazypipe.  This difference can be easily solved by passing a function on the lazypipe step

```js
var gulpif = require('gulp-if');
var jshint = require('gulp-jshint');
var uglify = require('gulp-uglify');
var lazypipe = require('lazypipe');

var compressing = false;

var jsChannel = lazypipe()
  // adding a pipeline step
  .pipe(jshint) // notice the stream function has not been called!
  .pipe(jshint.reporter)
  // adding a step with an argument
  .pipe(jshint.reporter, 'fail')
  // you can't say: .pipe(gulpif, compressing, uglify)
  // because uglify needs to be instantiated separately in each lazypipe instance
  // you can say this instead:
  .pipe(function () {
    return gulpif(compressing, uglify());
  });
  // why does this work? lazypipe calls the function, passing in the no arguments to it,
  // it instantiates a new gulp-if pipe and returns it to lazypipe.

gulp.task('scripts', function () {
  return gulp.src(paths.scripts.src)
    .pipe(jsChannel())
    .pipe(gulp.dest(paths.scripts.dest));
});
```

[source](https://github.com/robrich/gulp-if/issues/32)

## gulp-if API

### gulpif\(condition, stream \[, elseStream, \[, minimatchOptions\]\]\)

gulp-if will pipe data to `stream` whenever `condition` is truthy.

If `condition` is falsey and `elseStream` is passed, data will pipe to `elseStream`

After data is piped to `stream` or `elseStream` or neither, data is piped down-stream.

#### Parameters

##### condition

Type: `boolean` or [`stat`](http://nodejs.org/api/fs.html#fs_class_fs_stats) object or `function` that takes in a vinyl file and returns a boolean or `RegularExpression` that works on the `file.path`

The condition parameter is any of the conditions supported by [gulp-match](https://github.com/robrich/gulp-match).  The `file.path` is passed into `gulp-match`.

If a function is given, then the function is passed a vinyl `file`. The function should return a `boolean`.

##### stream

Stream for gulp-if to pipe data into when condition is truthy.

##### elseStream

Optional, Stream for gulp-if to pipe data into when condition is falsey.

##### minimatchOptions

Optional, if it's a glob condition, these options are passed to [minimatch](https://github.com/isaacs/minimatch).

