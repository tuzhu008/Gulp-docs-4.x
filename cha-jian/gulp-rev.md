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
| base | string | process.cwd\(\) | 覆盖 manifest 文件的 `base`。 |
| cwd | string | process.cwd\(\) | 覆盖 manifest 文件的当前工作目录。 |
| merge | boolean | false | 合并现有  manifest 文件。 |
| transformer | object | JSON | 具有 parse 和 stringify 方法的对象。这可以用来为 manifest 文件提供自定义转换器，而不是默认的 JSON。 |



### Original path

原始文件路径存储在 file.revorigpath 中。这对于重写对资产的引用之类的事情很有用。

`parse`

