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

包含内容是推荐的方式，因为它 "just works"。当将此设置为 `false`时，您必须宿主（host）源文件并正确设置 `sourceRoot`。

* `sourceRoot`

  Set the location where the source files are hosted \(use this when`includeContent`is set to`false`\). This is usually a URL \(or an absolute URL path\), not a local file system path. By default the source root is '' or in case`destPath`is set, the relative path from the source map to the source base directory \(this should work for many dev environments\). If a relative path is used \(empty string or one starting with a`.`\), it is interpreted as a path relative to the destination. The plugin rewrites it to a path relative to each source map.

  例如：

  ```

  ```

  Example \(using a function\):

  ```

  ```

  Example \(relative path\):

  ```

  ```

  In this case for a file written to`dist/subdir/example.js`, the source map is written to`dist/subdir/example.js.map`and the sourceRoot will be`../../src`\(resulting in the full source path`../../src/subdir/example.js`\).

* `destPath`

  Set the destination path \(the same you pass to`gulp.dest()`\). If the source map destination path is not a sub path of the destination path, this is needed to get the correct path in the`file`property of the source map. In addition, it allows to automatically set a relative`sourceRoot`if none is set explicitly.

* `sourceMappingURLPrefix`

  Specify a prefix to be prepended onto the source map URL when writing external source maps. Relative paths will have their leading dots stripped.

  例如：

  ```js

  ```

  This will result in a source mapping URL comment like`sourceMappingURL=https://asset-host.example.com/assets/maps/helloworld.js.map`.

* `sourceMappingURL`

  If you need full control over the source map URL you can pass a function to this option. The output of the function must be the full URL to the source map \(in function of the output file\).

  例如：

  ```js

  ```

  This will result in a source mapping URL comment like`sourceMappingURL=https://asset-host.example.com/helloworld.js.map`.

* `mapFile`

  This option allows to rename the map file. It takes a function that is called for every map and receives the default map path as a parameter.

  例如：

  ```js

  ```

* `mapSources`

  **This option is deprecated. Upgrade to use our**[`sourcemap.mapSources`](https://github.com/gulp-sourcemaps/gulp-sourcemaps#alter-sources-property-on-sourcemaps)**API.**

* `charset`

  Sets the charset for inline source maps. Default:`utf8`

* `clone`

  Clones the original file for creation of the map file. Could be important if file history is important. See[file.clone\(\)](https://github.com/gulpjs/vinyl#filecloneoptions)for possible options. Default:`{deep:false, contents:false}`

### Plugin developers only:

* **How to add source map support to plugins**

  * Generate a source map for the transformation the plugin is applying
  * **Important**
    : Make sure the paths in the generated source map \(
    `file`
    and
    `sources`
    \) are relative to
    `file.base`
    \(e.g. use
    `file.relative`
    \).
  * Apply this source map to the vinyl
    `file`
    . E.g. by using
    [vinyl-sourcemaps-apply](https://github.com/gulp-sourcemaps/vinyl-sourcemaps-apply)
    . This combines the source map of this plugin with the source maps coming from plugins further up the chain.
  * Add your plugin to the
    [wiki page](https://github.com/gulp-sourcemaps/gulp-sourcemaps/wiki/Plugins-with-gulp-sourcemaps-support)

  #### 例如：

  ```js

  ```

  * **Verify sourcemaps are working**

    See example below or refer to[test/write.js](https://github.com/gulp-sourcemaps/gulp-sourcemaps/blob/master/test/write.js)

  #### 例如：

  ```js

  ```



