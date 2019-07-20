## 简介

Gulp 插件，通过将内容哈希附加到文件名来对静态资产进行修正：`unicorn.css` → `unicorn-d41d8cd98f.css`

[github 地址](https://github.com/sindresorhus/gulp-rev)

## 安装

```
npm install --save-dev gulp-rev
```

## 用法

```js
const gulp = require('gulp');
const rev = require('gulp-rev');

function defaultTask() {
  return gulp.src('src/*.css')
    .pipe(rev())
    .pipe(gulp.dest('dist'))
}
```

## API

### rev\(\)

### rev.manifest\(\[path\], \[options\]\)

#### path

类型：·`string`

默认值：`rev-manifest.json`

Manifest 文件 path

#### options

| 属性 | 类型 | 默认值 | 描述 |
| :--- | :--- | :--- | :--- |
| `base` | string | `process.cwd()` | 覆盖 manifest 文件的 `base`。 |
| `cwd` | string | `process.cwd()` | 覆盖 manifest 文件的当前工作目录。 |
| `merge` | boolean | `false` | 合并现有 manifest 文件。 |
| `transformer` | object | `JSON` | 具有 `parse` 和 `stringify` 方法的对象。这可以用来为 manifest 文件提供自定义转换器，而不是默认的 JSON。 |

### 原始路径

原始文件路径存储在 `file.revOrigPath` 中。这对于重写对资源的引用之类的事情很有用。

### 资源清单

```js
function defaultTask() {
  // 默认情况下，Gulp 会选择 `assets/css` 作为基础路径，所以我们需要显式地设置它:
  return gulp.src(['assets/css/*.css', 'assets/js/*.js'], {base: 'assets'})
    .pipe(gulp.dest('build/assets'))  // copy original assets to build dir
    .pipe(rev())
    .pipe(gulp.dest('build/assets'))  // write rev'd assets to build dir
    .pipe(rev.manifest())
    .pipe(gulp.dest('build/assets'))  // write manifest to build dir
}
```

将原始路径映射到修正后的路径的资源清单写入 `build/assets/rev-manifest.json`：

```js
{
  "css/unicorn.css": "css/unicorn-d41d8cd98f.css",
  "js/unicorn.js": "js/unicorn-273c2c123f.js"
}
```

默认情况下，`rev-manifest.json` 将作为一个整体被替换。若要与现有清单合并，请将 `merge: true` 和输出目标\(作为 `base`\)传递给 `rev.manifest()` ：

```js
const gulp = require('gulp');
const rev = require('gulp-rev');

function defaultTask() {
  // 默认情况下，Gulp 会选择 `assets/css` 作为基础路径，所以我们需要显式地设置它:
  return gulp.src(['assets/css/*.css', 'assets/js/*.js'], {base: 'assets'})
    .pipe(gulp.dest('build/assets'))
    .pipe(rev())
    .pipe(gulp.dest('build/assets'))
    .pipe(rev.manifest({
      base: 'build/assets',
      merge: true // 如果现有清单存在，则与之合并
    }))
    .pipe(gulp.dest('build/assets'))
}
```

您可以选择调用 `rev.manifest('manifest.json')` 给它一个不同的路径或文件名。

## Sourcemaps and`gulp-concat`

由于 `gulp-concat` 处理文件路径的方式，您可能需要在 `gulp-concat` 实例上手动设置 `cwd` 和 `path`，以使一切正常工作:

```js
const gulp = require('gulp');
const rev = require('gulp-rev');
const sourcemaps = require('gulp-sourcemaps');
const concat = require('gulp-concat');

function defaultTask() {
  return gulp.src('src/*.js')
    .pipe(sourcemaps.init())
    .pipe(concat({path: 'bundle.js', cwd: ''}))
    .pipe(rev())
    .pipe(sourcemaps.write('.'))
    .pipe(gulp.dest('dist'))
}
```

## 未更改文件的哈希值不同

由于流的顺序没有得到保证，所以一些插件\(如 `gulp-concat`\)可能导致最终文件的内容和散列发生更改。若要避免为未更改的源文件生成新的散列，可以:

* 使用 gulp-sort 对流进行排序

* 
用吸管把溪流分类

使用gulp- modified过滤未更改的文件

阅读更多关于增量构建的内容



