## 简介

Gulp 插件，用于过滤为更改的文件。

默认情况下，它只能检测流中的文件是否更改。如果您需要一些更高级的东西，比如知道导入/依赖关系是否发生了变化，创建一个自定义比较器，或者使用[另一个插件](//api/last-run.md)。

[github 地址](https://github.com/sindresorhus/gulp-changed#readme)

## 安装

```
npm install --save-dev gulp-changed
```

## 用法

```js
const gulp = require('gulp');
const changed = require('gulp-changed');
const ngAnnotate = require('gulp-ng-annotate'); // Just as an example

const SOURCE = 'src/*.js';
const DESTINATION = 'dist';

function defaultTask() {
  return gulp.src(SOURCE)
    .pipe(changed(DESTINATION))
    // `ngAnnotate` will only get the files that
    // changed since the last time it was run
    .pipe(ngAnnotate())
    .pipe(gulp.dest(DESTINATION))
}
```



