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
| options | object |详情请见下文 [选项][options-section] 。 |

### 返回值

Each object may have a `type` property that can be used to determine if the node is a `task` or `function`.

Each object may have a `branch` property that, when `true`, indicates the node was created using `series()` or `parallel()`.

返回一个详细描述已注册的任务树的对象——包含具有 `'label'` 和 `'nodes'` 属性的嵌套对象(与 [archy][archy-external] 兼容)。

每个对象可能有一个 `type` 属性，用于确定节点是 `task` 还是 `function`。

每个对象可能有一个“branch”属性，当“true”时，该属性指示节点是使用“series()”或“parallel()”创建的。

### 选项

| name | type | default | note |
|:-------:|:-------:|------------|--------|
| deep | boolean | false | If true, the entire tree will be returned. When false, only top level tasks will be returned. |

[options-section]: #options
[archy-external]: https://www.npmjs.com/package/archy
