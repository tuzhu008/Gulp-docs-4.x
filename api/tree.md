<!-- front-matter
id: tree
title: tree()
hide_title: true
sidebar_label: tree()
-->

# tree()

获取当前任务依赖关系树——在极少数情况下需要它。

通常，gulp 使用者不会使用 `tree()`，但它是公开的，因此 CLI 可以显示在 gulpfile 中定义的任务的依赖关系图。

## 用法

Example gulpfile:
```js

const { series, parallel } = require('gulp');

function one(cb) {
  // body omitted
  cb();
}

function two(cb) {
  // body omitted
  cb();
}

function three(cb) {
  // body omitted
  cb();
}

const four = series(one, two);

const five = series(four,
  parallel(three, function(cb) {
    // Body omitted
    cb();
  })
);

module.exports = { one, two, three, four, five };
```

`tree()` 的输出:
```js
{
  label: 'Tasks',
  nodes: [ 'one', 'two', 'three', 'four', 'five' ]
}
```


`tree({ deep: true })` 的输出:

```js
{
  label: "Tasks",
  nodes: [
    {
      label: "one",
      type: "task",
      nodes: []
    },
    {
      label: "two",
      type: "task",
      nodes: []
    },
    {
      label: "three",
      type: "task",
      nodes: []
    },
    {
      label: "four",
      type: "task",
      nodes: [
        {
          label: "<series>",
          type: "function",
          branch: true,
          nodes: [
            {
              label: "one",
              type: "function",
              nodes: []
            },
            {
              label: "two",
              type: "function",
              nodes: []
            }
          ]
        }
      ]
    },
    {
      label: "five",
      type: "task",
      nodes: [
        {
          label: "<series>",
          type: "function",
          branch: true,
          nodes: [
            {
              label: "<series>",
              type: "function",
              branch: true,
              nodes: [
                {
                  label: "one",
                  type: "function",
                  nodes: []
                },
                {
                  label: "two",
                  type: "function",
                  nodes: []
                }
              ]
            },
            {
              label: "<parallel>",
              type: "function",
              branch: true,
              nodes: [
                {
                  label: "three",
                  type: "function",
                  nodes: []
                },
                {
                  label: "<anonymous>",
                  type: "function",
                  nodes: []
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

## 签名

```js
tree([options])
```

### 参数

| 参数 | 类型 | 描述 |
|:--------------:|------:|--------|
| options | object | Detailed in [Options][options-section] below. |

### 返回值

An object detailing the tree of registered tasks - containing nested objects with `'label'` and `'nodes'` properties (which is [archy][archy-external] compatible).

Each object may have a `type` property that can be used to determine if the node is a `task` or `function`.

Each object may have a `branch` property that, when `true`, indicates the node was created using `series()` or `parallel()`.

### 选项

| name | type | default | note |
|:-------:|:-------:|------------|--------|
| deep | boolean | false | If true, the entire tree will be returned. When false, only top level tasks will be returned. |

[options-section]: #options
[archy-external]: https://www.npmjs.com/package/archy
