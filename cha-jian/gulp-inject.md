## 简介

Gulp 插件，用于 javascript、样式表和 webComponent 注入。

[github 地址](https://github.com/klei/gulp-inject)

## 安装

```
$ npm install --save-dev gulp-inject
```

## 用法

目标文件** **`src/index.html`**:**

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

`gulpfile.js`** 文件：**

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

## 更多例子

### 相对于目标文件注入文件

By default the injected file paths are relative to each source file's `cwd` \(see [`options.ignorePath`](#optionsignorepath)\). If `options.relative` is set to `true` each injected path will be relative to each target file's directory instead.

**Project structure:**

```
└── src
    ├── module
    │   ├── module.js
    │   └── module.html
    └── app
        ├── main.js
        └── index.html
```

`src/app/index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Index</title>
</head>
<body>
  <h1>Home</h1>
  <!-- inject:js -->
  <!-- endinject -->
</body>
</html>
```

`src/module/module.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Module</title>
</head>
<body>
  <h1>Module</h1>
  <!-- inject:js -->
  <!-- endinject -->
</body>
</html>
```

`gulpfile.js`**:**

```javascript
var inject = require('gulp-inject');

gulp.src('./src/**/*.html')
  .pipe(inject(gulp.src('./src/**/*.js', {read: false}), {relative: true}))
  .pipe(gulp.dest('./src'));
```

**Resulting **`src/app/index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Index</title>
</head>
<body>
  <h1>Home</h1>
  <!-- inject:js -->
  <script src="main.js"></script>
  <script src="../module/module.js"></script>
  <!-- endinject -->
</body>
</html>
```

**Resulting **`src/module/module.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Module</title>
</head>
<body>
  <h1>Home</h1>
  <!-- inject:js -->
  <script src="../app/main.js"></script>
  <script src="module.js"></script>
  <!-- endinject -->
</body>
</html>
```

### 从多个源流注入文件

This example demonstrates how to inject files from multiple different streams into the same injection placeholder.

Install [`event-stream`](https://www.npmjs.org/package/event-stream) with: `npm install --save-dev event-stream` and use its [`merge`](https://github.com/dominictarr/event-stream#merge-stream1streamn) function.

**Code:**

```javascript
var es = require('event-stream'),
    inject = require('gulp-inject'),
    concat = require('gulp-concat'),
    uglify = require('gulp-uglify');

// Concatenate vendor scripts
var vendorStream = gulp.src(['./src/vendors/*.js'])
  .pipe(concat('vendors.js'))
  .pipe(gulp.dest('./dist'));

// Concatenate AND minify app sources
var appStream = gulp.src(['./src/app/*.js'])
  .pipe(concat('app.js'))
  .pipe(uglify())
  .pipe(gulp.dest('./dist'));

gulp.src('./src/index.html')
  .pipe(inject(es.merge(vendorStream, appStream)))
  .pipe(gulp.dest('./dist'));
```

#### Multiple sources when order is important

Use [`stream-series`](https://github.com/rschmukler/stream-series).

**Code:**

```javascript
var series = require('stream-series'),
    inject = require('gulp-inject');

var vendorStream = gulp.src(['./src/vendors/*.js'], {read: false});

var appStream = gulp.src(['./src/app/*.js'], {read: false});

gulp.src('./src/index.html')
  .pipe(inject(series(vendorStream, appStream))) // This will always inject vendor files before app files
  .pipe(gulp.dest('./dist'));
```

### Injecting some files into `<head>` and some into `<body>`

#### Method 1: Use `gulp-inject`'s `starttag` option.

`gulpfile.js`**:**

```javascript
var inject = require('gulp-inject');

gulp.src('./src/index.html')
  .pipe(inject(gulp.src('./src/importantFile.js', {read: false}), {starttag: '<!-- inject:head:{{ext}} -->'}))
  .pipe(inject(gulp.src(['./src/*.js', '!./src/importantFile.js'], {read: false})))
  .pipe(gulp.dest('./dist'));
```

**And in your **`./src/index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- inject:head:js -->
  <!-- only importantFile.js will be injected here -->
  <!-- endinject -->
</head>
<body>

  <!-- inject:js -->
  <!-- the rest of the *.js files will be injected here -->
  <!-- endinject -->
</body>
</html>
```

#### Method 2: Use `gulp-inject`'s `name` option.

`gulpfile.js`**:**

```javascript
var inject = require('gulp-inject');

gulp.src('./src/index.html')
  .pipe(inject(gulp.src('./src/importantFile.js', {read: false}), {name: 'head'}))
  .pipe(inject(gulp.src(['./src/*.js', '!./src/importantFile.js'], {read: false})))
  .pipe(gulp.dest('./dist'));
```

**And in your **`./src/index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- head:js -->
  <!-- only importantFile.js will be injected here -->
  <!-- endinject -->
</head>
<body>

  <!-- inject:js -->
  <!-- the rest of the *.js files will be injected here -->
  <!-- endinject -->
</body>
</html>
```

### Injecting all files for development

If you use [Bower](http://bower.io/) for frontend dependencies I recommend using [`main-bower-files`](https://www.npmjs.org/package/main-bower-files) and injecting them as well.

`gulpfile.js`**:**

```javascript
var bowerFiles = require('main-bower-files'),
    inject = require('gulp-inject'),
    stylus = require('gulp-stylus'),
    es = require('event-stream');

var cssFiles = gulp.src('./src/**/*.styl')
  .pipe(stylus())
  .pipe(gulp.dest('./build'));

gulp.src('./src/index.html')
  .pipe(inject(gulp.src(bowerFiles(), {read: false}), {name: 'bower'}))
  .pipe(inject(es.merge(
    cssFiles,
    gulp.src('./src/app/**/*.js', {read: false})
  )))
  .pipe(gulp.dest('./build'));
```

`src/index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- bower:css -->
  <!-- bower installed css files will go here... -->
  <!-- endinject -->
  <!-- inject:css -->
  <!-- built css files will go here... -->
  <!-- endinject -->
</head>
<body>

  <!-- bower:js -->
  <!-- bower installed scripts will go here... -->
  <!-- endinject -->
  <!-- inject:js -->
  <!-- app scripts will go here... -->
  <!-- endinject -->
</body>
</html>
```

**Note** remember to mount `./bower_components`, `./build` and `./src/app` as static resources in your server to make this work.

### Injecting AngularJS scripts for development

If you're writing an AngularJS application and follow [Google's Angular APP Structure Recommendations](https://docs.google.com/document/d/1XXMvReO8-Awi1EZXAXS4PzDzdNvV6pGcuaF4Q9821Es/pub), which I think you should, it's important that the script files are injected in the correct order to avoid module instantiation problems like `Uncaught Error: [$injector:modulerr]`.

To do this you can use [`gulp-angular-filesort`](https://www.npmjs.org/package/gulp-angular-filesort) together with `gulp-inject` like so:

```javascript
var angularFilesort = require('gulp-angular-filesort'),
    inject = require('gulp-inject');

gulp.src('./src/index.html')
  .pipe(inject(
    gulp.src('./src/app/**/*.js') // gulp-angular-filesort depends on file contents, so don't use {read: false} here
      .pipe(angularFilesort())
    ))
  .pipe(gulp.dest('./build'));
```

### Injecting into a json-file

You can customize `gulp-inject` further by using the `transform` function option, e.g. by injecting files into a json-file.

**Code:**

```javascript
gulp.src('./files.json')
  .pipe(inject(gulp.src(['./src/*.js', './src/*.css', './src/*.html'], {read: false}), {
    starttag: '"{{ext}}": [',
    endtag: ']',
    transform: function (filepath, file, i, length) {
      return '  "' + filepath + '"' + (i + 1 < length ? ',' : '');
    }
  }))
  .pipe(gulp.dest('./'));
```

Initial contents of `files.json`:

```json
{
  "js": [
  ],
  "css": [
  ],
  "html": [
  ]
}
```

### Injecting with custom `transform` function with default fallback

The [default `transform`](#injecttransform) function is available to use e.g. as a default fallback.

Used here to inject Word documents as `<a>` tags below:

`index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My documents</title>
</head>
<body>
  <h1>Documents</h1>
  <ul>
    <!-- inject:docx -->
    <!-- endinject -->
  </ul>
  <!-- inject:js -->
  <!-- endinject -->
</body>
</html>
```

`gulpfile.js`**:**

```javascript
var inject = require('gulp-inject');

gulp.src('./index.html')
  .pipe(inject(
    gulp.src(['./*.js', './docs/*.docx'], {read: false}), {
      transform: function (filepath) {
        if (filepath.slice(-5) === '.docx') {
          return '<li><a href="' + filepath + '">' + filepath + '</a></li>';
        }
        // Use the default transform as fallback:
        return inject.transform.apply(inject.transform, arguments);
      }
    }
  ))
  .pipe(gulp.dest('./'));
```

**Resulting **`index.html`**:**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My documents</title>
</head>
<body>
  <h1>Documents</h1>
  <ul>
    <!-- inject:docx -->
    <li><a href="/docs/document1.docx"></a></li>
    <li><a href="/docs/document2.docx"></a></li>
    <!-- endinject -->
  </ul>
  <!-- inject:js -->
  <script src="/lib1.js"></script>
  <script src="/lib2.js"></script>
  <!-- endinject -->
</body>
</html>
```

### Injecting dist files into bower.json's main section

**Code:**

```javascript
gulp.src('./bower.json')
  .pipe(inject(gulp.src(['./dist/app.min.js', './dist/app.min.css'], {read: false}), {
    starttag: '"main": [',
    endtag: ']',
    transform: function (filepath, file, i, length) {
      return '  "' + filepath + '"' + (i + 1 < length ? ',' : '');
    }
  }))
  .pipe(gulp.dest('./'));
```

### Injecting all javascript files into a karma config file

**Code:**

```javascript
gulp.src('./karma.conf.js')
  .pipe(inject(gulp.src(['./src/**/*.js'], {read: false}), {
    starttag: 'files: [',
    endtag: ']',
    transform: function (filepath, file, i, length) {
      return '  "' + filepath + '"' + (i + 1 < length ? ',' : '');
    }
  }))
  .pipe(gulp.dest('./'));
```

### Injecting files contents

In order to inject files contents you have to provide custom `transform` function, that will return file contents as string. You also have to omit `{read: false}` option of `gulp.src` in this case. Example below shows how to inject contents of html partials into head of `index.html`:

_**Code:**_

```javascript
gulp.src('./src/index.html')
  .pipe(inject(gulp.src(['./src/partials/head/*.html']), {
    starttag: '<!-- inject:head:{{ext}} -->',
    transform: function (filePath, file) {
      // return file contents as string
      return file.contents.toString('utf8')
    }
  }))
  .pipe(gulp.dest('./dest'));
```

And in your `./src/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- inject:head:html -->
  <!-- contents of html partials will be injected here -->
  <!-- endinject -->
</head>
<body>
</body>
</html>
```

### Injecting files contents based on file path

In order to inject files based on file path you have to provide custom `starttag` which includes `{{path}}`. Additionally, in order to inject file contents include `transform` function, that will return file contents as string. You also have to omit `{read: false}` option of `gulp.src` in this case. Path can be either absolute, or relative in which case you should set \[`options.relative`\] to true. Example below shows how to inject contents of html partials into `index.html`:

_**Code:**_

```javascript
gulp.src('./src/index.html')
  .pipe(inject(gulp.src(['./src/partials/head/*.html']), {
    starttag: '<!-- inject:{{path}} -->',
    relative: true,
    transform: function (filePath, file) {
      // return file contents as string
      return file.contents.toString('utf8')
    }
  }))
  .pipe(gulp.dest('./dest'));
```

And in your `./src/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
  <title>My index</title>
  <!-- inject:path/to/your/file.ext -->
  <!-- contents of html partials will be injected here -->
  <!-- endinject -->
</head>
<body>
</body>
</html>
```

## API

### inject\(sources, options\)

Parameter: `sources`  
Type: `Stream`

Provide a Vinyl File Stream as input to `inject`, see examples above.

Parameter: `options`  
Type: `Object`

For available options see [Options](#options)

### Options

#### options.ignorePath

Type: `String` or `Array`

Default: `NULL`

A path or paths that should be removed from each injected file path.

This could also be solved by setting the `cwd` option for your `gulp.src` streams, each source file's `cwd` is automatically removed from its path before injection \(if not [`options.relative`](#optionsrelative) is set to `true`, see below\).

#### options.relative

Type: `Boolean`

Default: `false`

If set to `true` paths for the injected files will be relative to each target file, this also means that each source file's `cwd` is not necessary to remove from its path.

#### options.addPrefix

Type: `String`

Default: `NULL`

A path that should be prefixed to each injected file path.

#### options.addSuffix

Type: `String`

Default: `NULL`

A path that should be suffixed to each injected file path.

#### options.addRootSlash

Type: `Boolean`

Default: [`!options.relative`](#optionsrelative)

The root slash is automatically added at the beginning of the path \('/'\), or removed if set to `false`.

#### options.name

Type: `String`

Default: `"inject"`

Used in the default [start](#optionsstarttag) and [end](#optionsendtag) tags below.

#### options.removeTags

Type: `Boolean`

Default: `false`

When `true` the start and end tags will be removed when injecting files.

#### options.empty

Type: `Boolean`

Default: `false`

When `true` all tags without corresponding files will be emptied.

[**Warning** this has the potential issue of emptying more than expected.](https://github.com/klei/gulp-inject/issues/135)

#### options.starttag

**Type:** `String`\|`Function(targetExt, sourceExt)`

**Params \(if function\):**

* `targetExt` - The file extension of the target file
* `sourceExt` - The file extension of source file

**Purpose:**

Used to dynamically set starting placeholder tag depending on file extensions.  
In the provided string, or the string returned from the given function, the string `{{ext}}` is replaced with the source file extension name, e.g. "css", "js" or "html". `{{name}}` will be replaced by [`options.name`](#optionsname). `{{path}}` will be replaced by path to source file \(when used together with \[`options.relative`\] it will allow relative path to source file.

##### Default:

A function dependent on target file type and source file type that returns:

* html as target: `<!-- {{name}}:{{ext}} -->`
* haml as target: `-# {{name}}:{{ext}}`
* jade as target: `//- {{name}}:{{ext}}`
* pug as target: `//- {{name}}:{{ext}}`
* jsx as target: `{/* {{name}}:{{ext}} */}`
* slm as target: `/ {{name}}:{{ext}}`
* less as target: `/* {{name}}:{{ext}} */`
* sass, scss as target: `/* {{name}}:{{ext}} */`

#### options.endtag

**Type:** `String`\|`Function(targetExt, sourceExt)`

**Params \(if function\):**

* `targetExt` - The file extension of the target file
* `sourceExt` - The file extension of source file

**Purpose:**

Used to dynamically set ending placeholder tag depending on file extensions.  
In the provided string, or the string returned from the given function, the string `{{ext}}` is replaced with the source file extension name, e.g. "css", "js" or "html". `{{name}}` will be replaced by [`options.name`](#optionsname). `{{path}}` will be replaced by path to source file.

##### Default:

A function dependent on target file type and source file type that returns:

* html as target: `<!-- endinject -->`
* haml as target: `-# endinject`
* jade as target: `//- endinject`
* pug as target: `//- endinject`
* jsx as target: `{/* endinject */}`
* slm as target: `/ endinject`
* less as target: `/* endinject */`
* sass, scss as target: `/* endinject */`

#### options.transform

**Type**: `Function(filepath, file, index, length, targetFile)`

**Params:**

* `filepath` - The "unixified" path to the file with any `ignorePath`'s removed, `addPrefix` and `addSuffix` added
* `file` - The [File object](https://github.com/wearefractal/vinyl) to inject given from `gulp.src`
* `index` - 0-based file index
* `length` - Total number of files to inject for the current file extension
* `targetFile` - The target [file](https://github.com/wearefractal/vinyl) to inject into

**Purpose:**

Used to generate the content to inject for each file.

##### Default:

[A function](#injecttransform) dependent on target file type and source file type that returns:

**Injecting into **`html`

* css files: `<link rel="stylesheet" href="<filename>.css">`
* js files: `<script src="<filename>.js"></script>`
* coffee files: `<script type="text/coffeescript" src="<filename>.coffee"></script>`
* html files: `<link rel="import" href="<filename>.html">`
* png files: `<img src="<filename>.png">`
* gif files: `<img src="<filename>.gif">`
* jpg files: `<img src="<filename>.jpg">`
* jpeg files: `<img src="<filename>.jpeg">`

If `options.selfClosingTag` is `true` the default transformer above will make the `<link>` and `<img>` tags self close, i.e: `<link ... />` and `<img ... />` respectively.

**Injecting into **`jsx`

The same as for injecting into `html` above with [`options.selfClosingTag`](#optionsselfclosingtag) set to `true`.

**Injecting into **`jade`

* css files: `link(rel="stylesheet", href="<filename>.css")`
* js files: `script(src="<filename>.js")`
* coffee files: `script(type="text/coffeescript", src="<filename>.coffee")`
* html files: `link(rel="import", href="<filename>.html")`
* png files: `img(src="<filename>.png")`
* gif files: `img(src="<filename>.gif")`
* jpg files: `img(src="<filename>.jpg")`
* jpeg files: `img(src="<filename>.jpeg")`

**Injecting into **`pug`

* css files: `link(rel="stylesheet", href="<filename>.css")`
* js files: `script(src="<filename>.js")`
* coffee files: `script(type="text/coffeescript", src="<filename>.coffee")`
* html files: `link(rel="import", href="<filename>.html")`
* png files: `img(src="<filename>.png")`
* gif files: `img(src="<filename>.gif")`
* jpg files: `img(src="<filename>.jpg")`
* jpeg files: `img(src="<filename>.jpeg")`

**Injecting into **`slm`

* css files: `link rel="stylesheet" href="<filename>.css"`
* js files: `script src="<filename>.js"`
* coffee files: `script type="text/coffeescript" src="<filename>.coffee"`
* html files: `link rel="import" href="<filename>.html"`
* png files: `img src="<filename>.png"`
* gif files: `img src="<filename>.gif"`
* jpg files: `img src="<filename>.jpg"`
* jpeg files: `img src="<filename>.jpeg"`

**Injecting into **`haml`

* css files: `%link{rel:"stylesheet", href:"<filename>.css"}`
* js files: `%script{src:"<filename>.js"}`
* coffee files: `%script{type:"text/coffeescript", src:"<filename>.coffee"}`
* html files: `%link{rel:"import", href:"<filename>.html"}`
* png files: `%img{src:"<filename>.png"}`
* gif files: `%img{src:"<filename>.gif"}`
* jpg files: `%img{src:"<filename>.jpg"}`
* jpeg files: `%img{src:"<filename>.jpeg"}`

**Injecting into **`less`

* css files: `@import "<filename>.css";`
* less files: `@import "<filename>.less";`

**Injecting into **`scss`

* css files: `@import "<filename>.css";`
* scss files: `@import "<filename>.scss";`
* sass files: `@import "<filename>.sass";`

**Injecting into **`sass`

* css files: `@import "<filename>.css"`
* sass files: `@import "<filename>.sass"`
* scss files: `@import "<filename>.scss"`

#### options.selfClosingTag

Type: `Boolean`

Default: `false`

Affects the default `options.transform` function, see above.

#### options.quiet

Type: `Boolean`

Default: `false`

Lower the verbosity by setting this to true, suppressing the logging of successful injections.

#### ~~options.templateString~~

_**DEPRECATED!**_

_Deprecated since _`v.1.0`_. Use _[`gulp-file`](https://www.npmjs.org/package/gulp-file)_ instead:_

```javascript
var gulp = require('gulp');
var file = require('gulp-file');
var inject = require('gulp-inject');

file('index.html', '<html><head></head></html>')
  .pipe(inject(gulp.src(['./src/app/**/*.js']), {
    starttag: '<head>',
    endtag: '</head>'
  }))
  .pipe(gulp.dest('./dest'));
```

#### ~~options.sort~~

_**DEPRECATED!**_

_Deprecated since _`v.1.0`_. Use _[`sort-stream`](https://www.npmjs.org/package/sort-stream)_ instead:_

```javascript
var gulp = require('gulp');
var sort = require('sort-stream');
var inject = require('gulp-inject');

gulp.src('index.html')
  .pipe(inject(
    gulp.src(['./src/app/**/*.js'])
      .pipe(sort(function (a, b) {
      // Sort condition here...
      }))
  ))
  .pipe(gulp.dest('./dest'));
```

### inject.transform

The default transform function is exposed in the public API.

For more details see [the code with tests](https://github.com/klei/gulp-inject/tree/master/src/transform).

##### inject.transform.html

The default transform function for files into `html`, or other file types not `jade`, `pug`, `jsx`, `slm`, `less`, `scss`, `sass` or `haml`.

##### inject.transform.jade

The default transform function for files into `jade`.

##### inject.transform.pug

The default transform function for files into `pug`.

##### inject.transform.jsx

The default transform function for files into `jsx`.

##### inject.transform.slm

The default transform function for files into `slm`.

##### inject.transform.haml

The default transform function for files into `haml`.

##### inject.transform.less

The default transform function for files into `less`.

##### inject.transform.sass

The default transform function for files into `sass`.

##### inject.transform.scss

The default transform function for files into `scss`.



