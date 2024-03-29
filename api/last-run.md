<!-- front-matter
id: lastrun
title: lastRun()
hide_title: true
sidebar_label: lastRun()
-->

# lastRun()

检索在当前运行进程中成功完成任务的最后一次时间。最有用的后续任务运行时，监视程序正在运行。当监视程序正在运行时，对于后续的任务运行最有用。

当与 `src()` 组合时，通过跳过自上次成功完成任务以来没有更  改的文件，使增量构建能够加快执行时间。

## 用法

```js
const { src, dest, lastRun, watch } = require('gulp');
const imagemin = require('gulp-imagemin');

function images() {
  return src('src/images/**/*.jpg', { since: lastRun(images) })
    .pipe(imagemin())
    .pipe(dest('build/img/'));
}

exports.default = function() {
  watch('src/images/**/*.jpg', images);
};
```


## 签名

```js
lastRun(task, [precision])
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:------:|-------|
| task<br>**(required)** | function<br>string | The task function or the string alias of a registered task. |
| precision | number | Default: `1000` on Node v0.10, `0` on Node v0.12+. Detailed in [Timestamp precision][timestamp-precision-section] section below. |

### 返回值

A timestamp (in milliseconds), matching the last completion time of the task. If the task has not been run or has failed, returns `undefined`.

To avoid an invalid state being cached, the returned value will be `undefined` if a task errors.

### Errors

When called with a value other than a string or function, throws an error with the message, "Only functions can check lastRun".

When called on a non-extensible function and Node is missing WeakMap, throws an error with the message, "Only extensible functions can check lastRun".

## 时间戳精度

While there are sensible defaults for the precision of timestamps, they can be rounded using the `precision` parameter. Useful if your file system or Node version has a lossy precision on file time attributes.

* `lastRun(someTask)` returns 1426000001111
* `lastRun(someTask, 100)` returns 1426000001100
* `lastRun(someTask, 1000)` returns 1426000001000

A file's [mtime stat][fs-stats-concepts] precision may vary depending on the node version and/or the file system used.


| platform | precision |
|:-----------:|:------------:|
| Node v0.10 | 1000ms |
| Node v0.12+ | 1ms |
| FAT32 file system | 2000ms |
| HFS+ or Ext3 file systems | 1000ms |
| NTFS using Node v0.10 | 1s |
| NTFS using Node 0.12+ | 100ms |
| Ext4 using Node v0.10 | 1000ms |
| Ext4 using Node 0.12+ | 1ms |


[timestamp-precision-section]: #timestamp-precision
[fs-stats-concepts]: ../api/concepts.md#file-system-stats
