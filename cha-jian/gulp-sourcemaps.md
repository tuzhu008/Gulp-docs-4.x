## 简介

Gulp 插件，为 gulp 提供 Sourcemap 支持。

[github 地址](https://github.com/gulp-sourcemaps/gulp-sourcemaps)

## 安装

```
npm install --save-dev gulp-sourcemaps
```

## 用法

#### 内联源映射

内联源映射是指将源映射嵌入到源文件中。

例如：

```js
var gulp = require('gulp');
var plugin1 = require('gulp-plugin1');
var plugin2 = require('gulp-plugin2');
var sourcemaps = require('gulp-sourcemaps');

function javascriptTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init())
      .pipe(plugin1())
      .pipe(plugin2())
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('dist'));
})
```

`sourcemaps.init()` 和 `sourcemaps.write()` 之间的所有插件都需要支持 gulp-sourcemaps。您可以在 [wiki](https://github.com/gulp-sourcemaps/gulp-sourcemaps/wiki/Plugins-with-gulp-sourcemaps-support) 中找到这样的插件列表。

#### 外部源映射

要生成外部源映射文件，请将相对于目标的路径传递给 `sourcemaps.write()`。

例如：

```js
var gulp = require('gulp');
var plugin1 = require('gulp-plugin1');
var plugin2 = require('gulp-plugin2');
var sourcemaps = require('gulp-sourcemaps');

function javascriptTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init())
      .pipe(plugin1())
      .pipe(plugin2())
    .pipe(sourcemaps.write('../maps'))
    .pipe(gulp.dest('dist'));
}
```

#### 加载现有源映射

要加载现有的源映射，请将 `loadMaps: true` 选项传递给 `sourcemaps.init()`。

例如：

```js
var gulp = require('gulp');
var plugin1 = require('gulp-plugin1');
var plugin2 = require('gulp-plugin2');
var sourcemaps = require('gulp-sourcemaps');

function javascriptTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init({loadMaps: true}))
      .pipe(plugin1())
      .pipe(plugin2())
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('dist'));
}
```

#### 处理大文件

要处理大文件，请将选项 `largeFile: true` 传递给 `sourcemaps.init()` 。

例如：

```js
var gulp = require('gulp');
var plugin1 = require('gulp-plugin1');
var plugin2 = require('gulp-plugin2');
var sourcemaps = require('gulp-sourcemaps');

function javascriptTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init({largeFile: true}))
      .pipe(plugin1())
      .pipe(plugin2())
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('dist'));
}
```

#### 处理来自不同目录的源文件

使用 `gulp.src` 上的 `base` 选项，以确保所有文件都相对于公共基本目录。

例如：

```js
var gulp = require('gulp');
var plugin1 = require('gulp-plugin1');
var plugin2 = require('gulp-plugin2');
var sourcemaps = require('gulp-sourcemaps');

function javascriptTask() {
  return gulp.src(['src/test.js', 'src/testdir/test2.js'], { base: 'src' })
    .pipe(sourcemaps.init())
      .pipe(plugin1())
      .pipe(plugin2())
    .pipe(sourcemaps.write('../maps'))
    .pipe(gulp.dest('dist'));
}
```

#### 更改源文件上的 `source` 属性

导出的 `mapSources` 方法可以完全控制源路径。它接受一个函数，该函数为每个源调用，并接收默认源路径和原始 vinyl 文件作为参数。

例如：

```js
function javascriptTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init())
      .pipe(plugin1())
      .pipe(plugin2())
      // be careful with the sources returned otherwise contents might not be loaded properly
      .pipe(sourcemaps.mapSources(function(sourcePath, file) {
        // source paths are prefixed with '../src/'
        return '../src/' + sourcePath;
      }))
    .pipe(sourcemaps.write('../maps')
    .pipe(gulp.dest('public/scripts'));
}
```

#### Generate Identity Sourcemap

导出的 `identityMap` 方法允许您生成一个完整的有效源映射，该映射不进行任何更改\(只对 Javascript 和 CSS 进行较慢的编码\)，而不是默认的空源映射\(无映射，快速\)。**如果您获取丢失或不正确的映射，请使用此选项，例如当 debugging 时。**

例如：

```js
function javascriptTask() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init())
      // An identity sourcemap will be generated at this step
      .pipe(sourcemaps.identityMap())
      .pipe(plugin1())
      .pipe(plugin2())
    .pipe(sourcemaps.write('../maps')
    .pipe(gulp.dest('public/scripts'));
}
```

### init 选项

* `loadMaps`

  设置为 `true` 可加载源文件的现有映射。支持以下:

* * 内联源映射
  * `sourceMappingURL=` 注释引用的源映射文件
  * 同一目录中具有相同名称\(plus.map\)的源映射文件
* `identityMap`

  **该选项已废弃。升级到 **[`sourcemap.identityMap`](https://github.com/gulp-sourcemaps/gulp-sourcemaps#generate-identity-sourcemap) **API.**

### Write 选项

* `addComment`

  默认情况下，添加一个包含/引用源映射的注释。将此设置为 `false` 以禁用注释\(例如，如果您想通过 header 加载源映射\)。

* 例如：

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write('../maps', {addComment: false}))
      .pipe(gulp.dest('dist'));
  }
  ```

* `includeContent`

  默认情况下，源映射包含源代码。设置为 `false` 使用原始文件。

  包含内容是推荐的方式，因为它 "just works"。当将此设置为 `false`时，您必须托管源文件并正确设置 `sourceRoot`。

* `sourceRoot`

 设置源文件托管的位置(当 `includeContent` 被设置为 `false` 时使用此设置)。这通常是一个 URL(或绝对 UR路径)，而不是本地文件系统路径。默认情况下，源根目录是 '' 或者在设置了 `destPath` 的情况下，从源映射到源根目录的相对路径(这应该适用于许多开发环境)。如果使用相对路径(空字符串或以 `.` 开头的字符串)，则将其解释为相对于目标的路径。插件将其重写为相对于每个源映射的路径。

  例如：

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write({includeContent: false, sourceRoot: '/src'}))
      .pipe(gulp.dest('dist'));
  }
  ```

  例如 \(使用函数\):

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write({
        includeContent: false,
        sourceRoot: function(file) {
          return '/src';
        }
      }))
      .pipe(gulp.dest('dist'));
  }
  ```

  例如 \(相对路径\):

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write('.', {includeContent: false, sourceRoot: '../src'}))
      .pipe(gulp.dest('dist'));
  }
  ```

  在本例中，为写入 `dist/subdir/example.js` 的文件，其源映射被写到 `dist/subdir/example.js.map`，其 sourceRoot 将是 `../../src`  (会生成完整的源路径 `../../src/subdir/example.js`)。

* `destPath`

  设置目标路径\(与传递给 `gulp.dest()` 的路径相同)。如果源映射目标路径不是目标路径的子路径，则需要在源映射的 `file` 属性中获得正确的路径。此外，如果没有显式设置相关的 `sourceRoot`，则允许自动设置。

* `sourceMappingURLPrefix`

  在编写外部源映射时，指定一个前缀作为源映射 URL 的前缀。相对路径的前导点（`.`）将被剥离。

  例如：

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write('../maps', {
        sourceMappingURLPrefix: 'https://asset-host.example.com/assets'
      }))
      .pipe(gulp.dest('public/scripts'));
  }
  ```

  这将生成如下源映射 URL 注释：`sourceMappingURL=https://asset-host.example.com/assets/maps/helloworld.js.map`

* `sourceMappingURL`

  如果需要完全控制源映射 URL，可以将函数传递给该选项。函数的输出必须是源映射的完整 URL(在输出文件的函数中)。

  例如：

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write('../maps', {
        sourceMappingURL: function(file) {
          return 'https://asset-host.example.com/' + file.relative + '.map';
        }
      }))
      .pipe(gulp.dest('public/scripts'));
  }
  ```

  这将生成如下源映射 URL 注释：`sourceMappingURL=https://asset-host.example.com/helloworld.js.map`.

* `mapFile`

  此选项允许重命名映射文件。它接受为每个映射调用的函数，并接收默认映射路径作为参数。

  例如：

  ```js
  function javascriptTask() {
    return gulp.src('src/**/*.js')
      .pipe(sourcemaps.init())
        .pipe(plugin1())
        .pipe(plugin2())
      .pipe(sourcemaps.write('../maps', {
        mapFile: function(mapFilePath) {
          // source map files are named *.map instead of *.js.map
          return mapFilePath.replace('.js.map', '.map');
        }
      }))
      .pipe(gulp.dest('public/scripts'));
  }
  ```

* `mapSources`

  **该选项已废弃。升级到**[`sourcemap.mapSources`](https://github.com/gulp-sourcemaps/gulp-sourcemaps#alter-sources-property-on-sourcemaps) **API.**

* `charset`

  为内联源映射设置字符集。默认为：`utf8`

* `clone`

  克隆原始文件以创建映射文件。如果文件历史记录很重要，那么它可能很重要。参见 [file.clone\(\)](https://github.com/gulpjs/vinyl#filecloneoptions) 获取可能的选项。 默认为：`{deep:false, contents:false}`

### 插件开发者：

* **如何向插件添加源映射支持**

  * 为正在应用的插件的转换生成源映射
  * 重要提示:确保生成的源映射(`file` 和 `sources`)中的路径是相对于 `file.base` 的(例如，使用`file.relative`)。
  * 将此源映射应用到 vinyl 文件。例如，使用 [vinyl-sourcemaps-apply](https://github.com/gulp-sourcemaps/vinyl-sourcemaps-apply)。这将此插件的源映射与来自更高层插件的源映射相组合。
  * 将插件添加到
    [wiki page](https://github.com/gulp-sourcemaps/gulp-sourcemaps/wiki/Plugins-with-gulp-sourcemaps-support)

  #### 例如：

  ```js
  var through = require('through2');
  var applySourceMap = require('vinyl-sourcemaps-apply');
  var myTransform = require('myTransform');

  module.exports = function(options) {

    function transform(file, encoding, callback) {
      // generate source maps if plugin source-map present
      if (file.sourceMap) {
        options.makeSourceMaps = true;
      }

      // do normal plugin logic
      var result = myTransform(file.contents, options);
      file.contents = new Buffer(result.code);

      // apply source map to the chain
      if (file.sourceMap) {
        applySourceMap(file, result.map);
      }

      this.push(file);
      callback();
    }

    return through.obj(transform);
  };
  ```

  * **V验证 sourcemaps 正在工作**

    参见下面的示例或参考：[test/write.js](https://github.com/gulp-sourcemaps/gulp-sourcemaps/blob/master/test/write.js)

  #### 例如：

  ```js
  var stream = plugin();
  var init = sourcemaps.init();
  var write = sourcemaps.write();

  init.pipe(stream).pipe(write);

  write.on('data', function (file) {
    assert(...);
    cb();
  });

  init.write(new gutil.File(...));
  init.end();
  ```
