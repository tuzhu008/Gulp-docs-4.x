## 简介

Gulp 插件，该插件使用 glob 模式过滤原始文件的子集。当您完成并希望返回所有原始文件时，只需`restore` 流;

[github 地址](https://github.com/sindresorhus/gulp-filter)

## 安装

```
$ npm install --save-dev gulp-filter
```


## Usage

### Filter only

只过滤流内容：

```js
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const filter = require('gulp-filter');

gulp.task('default', () => {
	// Create filter instance inside task function
	const f = filter(['**', '!*src/vendor']);
	
	return gulp.src('src/**/*.js')
		// Filter a subset of the files
		.pipe(f)
		// Run them through a plugin
		.pipe(uglify())
		.pipe(gulp.dest('dist'));
});
```

### 恢复被过滤的文件

```js
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const filter = require('gulp-filter');

gulp.task('default', () => {
	// Create filter instance inside task function
	const f = filter(['**', '!*src/vendor'], {restore: true});

	return gulp.src('src/**/*.js')
		// Filter a subset of the files
		.pipe(f)
		// Run them through a plugin
		.pipe(uglify())
		// Bring back the previously filtered out files (optional)
		.pipe(f.restore)
		.pipe(gulp.dest('dist'));
});
```

### 多过滤器

By combining and restoring different filters you can process different sets of files with a single pipeline.

```js
const gulp = require('gulp');
const less = require('gulp-less');
const concat = require('gulp-concat');
const filter = require('gulp-filter');

gulp.task('default', () => {
	const jsFilter = filter('**/*.js', {restore: true});
	const lessFilter = filter('**/*.less', {restore: true});

	return gulp.src('assets/**')
		.pipe(jsFilter)
		.pipe(concat('bundle.js'))
		.pipe(jsFilter.restore)
		.pipe(lessFilter)
		.pipe(less())
		.pipe(lessFilter.restore)
		.pipe(gulp.dest('out/'));
});
```

### 还原为文件源

You can restore filtered files in a different place and use it as a standalone source of files (ReadableStream). Setting the `passthrough` option to `false` allows you to do so.

```js
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const filter = require('gulp-filter');

gulp.task('default', () => {
	const f = filter(['**', '!*src/vendor'], {restore: true, passthrough: false});

	const stream = gulp.src('src/**/*.js')
		// Filter a subset of the files
		.pipe(f)
		// Run them through a plugin
		.pipe(uglify())
		.pipe(gulp.dest('dist'));

	// Use filtered files as a gulp file source
	f.restore.pipe(gulp.dest('vendor-dist'));

	return stream;
});
```


## API

### filter(pattern, [options])

Returns a [transform stream](https://nodejs.org/api/stream.html#stream_class_stream_transform) with a [.restore](#optionsrestore) property.

#### pattern

Type: `string | string[] | Function`

Accepts a string/array with globbing patterns which are run through [multimatch](https://github.com/sindresorhus/multimatch).

If you supply a function, you'll get a [`vinyl` file object](https://github.com/wearefractal/vinyl#file) as the first argument and you're expected to return a boolean of whether to include the file:

```js
filter(file => /unicorns/.test(file.path));
```

#### options

Type: `object`

Accepts [`minimatch` options](https://github.com/isaacs/minimatch#options).

*Note:* Set `dot: true` if you need to match files prefixed with a dot, for example, `.gitignore`.

##### restore

Type: `boolean`<br>
Default: `false`

Restore filtered files.

##### passthrough

Type: `boolean`<br>
Default: `true`

When set to `true`, filtered files are restored with a `stream.PassThrough`, otherwise, when set to `false`, filtered files are restored as a `stram.Readable`.

When the stream is a `stream.Readable`, it ends by itself, but when it's `stream.PassThrough`, you are responsible of ending the stream.




