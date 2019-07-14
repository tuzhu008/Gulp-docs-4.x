<!-- front-matter
id: vinyl-isvinyl
title: Vinyl.isVinyl()
hide_title: true
sidebar_label: Vinyl.isVinyl()
-->

# Vinyl.isVinyl()

使用这个方法而不是 `instanceof` 来确定对象是否是 Vinyl 实例。


**注意:**这个方法使用了一些旧版本 Vinyl 没有暴露的内部属性，如果使用过时的版本，会导致 false negative。

## 用法

```js
const Vinyl = require('vinyl');

const file = new Vinyl();
const notAFile = {};

Vinyl.isVinyl(file) === true;
Vinyl.isVinyl(notAFile) === false;
```

## 签名

```js
Vinyl.isVinyl(file);
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:------:|-------|
| file | object | The object to check. |

### 返回值

如果 `file` 对象是 Vinyl 实例，则为 True。
