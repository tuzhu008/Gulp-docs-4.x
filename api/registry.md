<!-- front-matter
id: registry
title: registry()
hide_title: true
sidebar_label: registry()
-->

# registry()

允许将自定义注册表插入任务系统，这可以提供共享任务或增强功能。

**注意：** 只有使用 `task()` 注册的任务才会提供给自定义的注册。将不提供直接传递给 `series()` 或 `parallel()` 的任务**函数**——如果需要自定义的注册表的行为，请使用字符串来组合任务。

在分配新注册表时，将传输当前注册表中的每个任务，并将用新注册表替换当前注册表。这允许按顺序添加多个自定义注册表。

有关详细信息，请参见[创建自定义注册](creating-custom-registries)中心。

## 用法

```js
const { registry, task, series } = require('gulp');
const FwdRef = require('undertaker-forward-reference');

registry(FwdRef());

task('default', series('forward-ref'));

task('forward-ref', function(cb) {
  // body omitted
  cb();
});
```

## 签名

```js
registry([registryInstance])
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|:-----:|--------|
| registryInstance | object |自定义注册表的实例(而不是类)。|

### 返回值

如果传递了 `registryInstance`，则不会返回任何内容。如果没有传递参数，则返回当前注册表实例。

### Errors

When a constructor (instead of an instance) is passed as `registryInstance`, throws an error with the message, "Custom registries must be instantiated, but it looks like you passed a constructor".

When a registry without a `get` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `get` function".

When a registry without a `set` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `set` function".

When a registry without an `init` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `init` function"

When a registry without a `tasks` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `tasks` function".

[creating-custom-registries]: ../documentation-missing.md
