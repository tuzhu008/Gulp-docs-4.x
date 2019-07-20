## 简介

这是与 [gulp-buffer](/cha-jian/gulp-buffer.md) 配套的 gulp 插件。

如果为了使用不支持流的 gulp 插件而需要使用[ gulp-buffer](/cha-jian/gulp-buffer.md)，那么现在的情况是所有内容都存储在内存缓冲区中。

在某些情况下，这可能是可以接受的，但是如果您的下游插件具有流功能，那么您也可能希望返回到内容的完全流模式。

这就是 gulp-stream 可以使用的地方：它确保所有缓冲区都被包装成可读的流。

[github 地址](https://github.com/sindresorhus/gulp-rev)

## 安装

```
npm install --save-dev gulp-stream
```

## 用法

```js

```



