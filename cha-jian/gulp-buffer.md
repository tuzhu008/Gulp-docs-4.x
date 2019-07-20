## 简介

Gulp 的插件，用于删除指定的文件或者文件夹。

一些 gulp 插件不支持流文件内容。gulp-buffer 和 gulp-stream 来救你。

gulp 插件不支持流的主要原因有三个:

* 流支持已经在路线图中，但是还没有添加到插件中
* 用于转换的底层库还支持流 API
* 内容转换不太适合流\(请考虑 `content.reverse()`，例如首先需要文件末尾的地方\)

在所有这些情况下，只需在这样的插件前面添加 gulp-buffer，它将确保插件仅显示缓冲区。

当你想切到完全的 streaming 功能，添加 gulp-stream，它将确保你所有的文件再次使用 streaming。

[github 地址](https://github.com/jeromew/gulp-buffer)

## 安装

```
npm install --save-dev gulp-buffer
```



