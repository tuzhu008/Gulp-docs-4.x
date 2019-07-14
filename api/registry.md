<!-- front-matter
id: registry
title: registry()
hide_title: true
sidebar_label: registry()
-->

# registry()


Allows custom registries to be plugged into the task system, which can provide shared tasks or augmented functionality.

**Note:** Only tasks registered with `task()` will be provided to the custom registry. The task functions passed directly to `series()` or `parallel()` will not be provided - if you need to customize the registry behavior, compose tasks with string references.

When assigning a new registry, each task from the current registry will be transferred and the current registry will be replaced with the new one. This allows for adding multiple custom registries in sequential order.

允许将自定义注册中心插入任务系统，该系统可以提供共享任务或增强功能。

**注意：**只有使用 `task()` 注册的任务才会提供给自定义 registry。将不提供直接传递给 `series()` 或 `parallel()` 的任务函数——如果需要自定义 registry 行为，请使用字符串引用组合任务。

在分配新 registry 时，将传输当前 registry 中的每个任务，并将用新 registry 替换当前 registry。这允许按顺序添加多个自定义 registry。

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
| registryInstance | object | An instance - not the class - of a custom registry. |

### 返回值

If a `registryInstance` is passed, nothing will be returned. If no arguments are passed, returns the current registry instance.

### Errors

When a constructor (instead of an instance) is passed as `registryInstance`, throws an error with the message, "Custom registries must be instantiated, but it looks like you passed a constructor".

When a registry without a `get` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `get` function".

When a registry without a `set` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `set` function".

When a registry without an `init` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `init` function"

When a registry without a `tasks` method is passed as `registryInstance`, throws an error with the message, "Custom registry must have `tasks` function".

[creating-custom-registries]: ../documentation-missing.md
