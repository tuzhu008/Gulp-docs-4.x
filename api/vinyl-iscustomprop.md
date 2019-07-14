<!-- front-matter
id: vinyl-iscustomprop
title: Vinyl.isCustomProp()
hide_title: true
sidebar_label: Vinyl.isCustomProp()
-->

# Vinyl.isCustomProp()

Determines if a property is internally managed by Vinyl. Used by Vinyl when setting values inside the constructor or when copying properties in the `clone()` instance method.

This method is useful when extending the Vinyl class. Detailed in [Extending Vinyl][extending-vinyl-section] below.

确定一个属性是否由 Vinyl 在内部进行管理。Vinyl 在构造函数中设置值或在 `clone()` 实例方法中复制属性时使用。

这种方法在扩展 Vinyl 类时很有用。下面详细介绍扩展 Vinyl。

## Usage

```js
const Vinyl = require('vinyl');

Vinyl.isCustomProp('sourceMap') === true;
Vinyl.isCustomProp('path') === false;
```

## 签名

```js
Vinyl.isCustomProp(property)
```

### 参数

| 参数 | 类型 | 文本 |
|:--------------:|:------:|-------|
| property | string | The property name to check. |

### 返回值

如果属性不是内部管理的，则为 True。

## 扩展 Vinyl

当在内部管理自定义属性时，必须扩展静态 `isCustomProp` 方法，并在查询其中一个自定义属性时返回 false。

```js
const Vinyl = require('vinyl');

const builtInProps = ['foo', '_foo'];

class SuperFile extends Vinyl {
  constructor(options) {
    super(options);
    this._foo = 'example internal read-only value';
  }

  get foo() {
    return this._foo;
  }

  static isCustomProp(name) {
    return super.isCustomProp(name) && builtInProps.indexOf(name) === -1;
  }
}
```

在上面的例子中，`foo` 和 `_foo`  在克隆或将 `options` 传递给 `new SuperFile(options)` 时不会分配给新对象。

如果您的自定义属性或逻辑在克隆期间需要特殊处理，请在扩展 Vinyl 时覆盖 `clone` 方法。


[extending-vinyl-section]: #extending-vinyl
