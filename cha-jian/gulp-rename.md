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



