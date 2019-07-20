## 简介

Gulp 的插件，用于删除指定的文件或者文件夹。

一些 gulp 插件不支持流文件内容。gulp-buffer 和 gulp-stream 来救你。

gulp 插件不支持流的主要原因有三个:

* The stream support is on the roadmap but has not been added to the plugin
* The underlying library used for the transformation does yet support a streaming API
* The content transformation is not very well suited for streaming \(think of content.reverse\(\) for example where you need the end of the file first\)

在所有这些情况下，只需在这样的插件前面添加gulp-buffer，它将确保插件只显示缓冲区。

当你想回到完全的流媒体功能，添加 gulp-stream，它将确保你所有的文件再次流媒体。

[github 地址](https://github.com/jeromew/gulp-buffer)

## 安装

```
npm install --save-dev gulp-buffer
```



