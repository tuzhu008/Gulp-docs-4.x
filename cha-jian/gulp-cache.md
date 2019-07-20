## 简介

Gulp 插件，用于缓存文件。

[github 地址](https://github.com/dlmanning/gulp-sass)

## 安装

```
npm install node-sass gulp-sass --save-dev
```

## 用法

该插件将在内存中缓存通过它的文件\(及其内容\)。如果文件在最后一次运行时已经通过，则不会向下传递。这意味着你只处理你需要的，节省时间和资源。

示例：

```js
var cache = require('gulp-cached');

function lintTask(){
  return gulp.src('files/*.js')
    .pipe(cache('linting'))
    .pipe(jshint())
    .pipe(jshint.reporter())
}

function watchTask(){
  gulp.watch('files/*.js', ['lintTask']);
}

const defaultTask = series(watchTask, lintTask);

```

* 用户保存 `files/a.js` 并调用 lint 任务
  * 这些文件在缓存中还不存在
  * `files/a.js`  和  `files/b.js` 被 linted

* 用户保存 `files/b.js` 并调用 lint 任务
  * 文件的内容与以前的值不同
  * `files/b.js` 被 linted

* 用户保存 `files/a.js` 并调用 lint 任务
  * 文件的内容没有与前一个值更改
  * 没有文件被 linted

因此，第一次运行将触发所有下游的项。之后的运行只会在它从最后一个以相同路径通过它的文件更改后才会触发。

请注意，不能与操作文件集的插件一起使用\(例如 [gulp-concat](/cha-jian/gulp-concat.md)\)。

### 清理缓存

#### 清除整个缓存

```js
cache.caches = {};
```

清除指定缓存

```js
delete cache.caches['cache name yo'];
```



