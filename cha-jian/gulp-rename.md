## 简介

Gulp 的文件重命名插件。

[github 地址](https://github.com/hparra/gulp-rename)

## 安装

```
npm install --save-dev gulp-rename
```

## 用法

`gulp-rename` 提供了简单的文件重命名方法。

```js
var rename = require("gulp-rename");

// 通过字符串重命名
gulp.src("./src/main/text/hello.txt")
  .pipe(rename("main/text/ciao/goodbye.md"))
  .pipe(gulp.dest("./dist")); // ./dist/main/text/ciao/goodbye.md

// 通过函数重命名
gulp.src("./src/**/hello.txt")
  .pipe(rename(function (path) {
    path.dirname += "/ciao";
    path.basename += "-goodbye";
    path.extname = ".md";
  }))
  .pipe(gulp.dest("./dist")); // ./dist/main/text/ciao/hello-goodbye.md

// 通过 hash 重命名
gulp.src("./src/main/text/hello.txt", { base: process.cwd() })
  .pipe(rename({
    dirname: "main/text/ciao",
    basename: "aloha",
    prefix: "bonjour-",
    suffix: "-hola",
    extname: ".md"
  }))
  .pipe(gulp.dest("./dist")); // ./dist/main/text/ciao/bonjour-aloha-hola.md
}
```

## 注意

* `dirname` 是由gulp设置的基本目录的相对路径。src到文件名。
  * `gulp.src()` 使用 glob-stream，它将第一个目录glob\(`*`、`**`、`[]` 或 extglob\)的基设置为父目录。`dirname` 是剩余的目录，或者 `./` 如果没有。glob-stream 版本 &gt;= 3.1.0 \(由 gulp &gt;= 3.2.2 使用\)接受一个 `base` 选项，该选项可用于显式设置 base。
  * `gulp.dest()` 重命名 `process.cwd()` 和 `dirname`\(即相对于 CWD 的基\)之间的目录。使用 `dirname` 重命名与 base 选项的 glob 或派生项匹配的目录。
    basename是没有类似路径扩展名的文件名

* `basename` 是没有类似路径扩展名的文件名。:文件名,path.extname\(文件名\)\)。

* `extname` 是文件扩展名，包括'。“就像path.extname\(文件名\)。

* 当使用函数时，第二个文件参数提供了整个上下文和原始文件值



