## 简介

Gulp 的插件，用于组装模块化的文件。

[github 地址](https://github.com/haoxins/gulp-file-include)

## 安装

```
npm install --save-dev gulp-file-include
```

## 选项

* options - type: `string`, just as prefix, default `@@`, and basepath is default `@file`

```js
fileinclude('@@')
```

* options - type: `object`

  * prefix: `string`, default `@@`
  * suffix: `string`, default `''`
  * basepath: `string`, default `@file`, it could be `@root`, `@file`, `your-basepath`
  * filters: `object`, filters of include content
  * context: `object`, context of `if` statement
  * indent: `boolean`, default `false`

* options.basepath - type: `string`, it could be

  * `@root`, include file relative to the dir where `gulp` running in
  * `@file`, include file relative to the dir where `file` in [example](example)
  * `your-basepath` include file relative to the basepath you give

```js
fileinclude({
  prefix: '@@',
  basepath: '@file'
})
```

```js
fileinclude({
  prefix: '@@',
  basepath: '/home/'
})
```

## 示例

* @@include options - type: `JSON`

index.html

```html
<!DOCTYPE html>
<html>
  <body>
  @@include('./view.html')
  @@include('./var.html', {
    "name": "haoxin",
    "age": 12345,
    "socials": {
      "fb": "facebook.com/include",
      "tw": "twitter.com/include"
    }
  })
  </body>
</html>
```

view.html

```html
<h1>view</h1>
```

var.html

```html
<label>@@name</label>
<label>@@age</label>
<strong>@@socials.fb</strong>
<strong>@@socials.tw</strong>
```

gulpfile.js

```js
var fileinclude = require('gulp-file-include'),
  gulp = require('gulp');

gulp.task('fileinclude', function() {
  gulp.src(['index.html'])
    .pipe(fileinclude({
      prefix: '@@',
      basepath: '@file'
    }))
    .pipe(gulp.dest('./'));
});
```

and the result is:

```html
<!DOCTYPE html>
<html>
  <body>
  <h1>view</h1>
  <label>haoxin</label>
<label>12345</label>
<strong>facebook.com/include</strong>
<strong>twitter.com/include</strong>
  </body>
</html>
```

### filters

```html
<!DOCTYPE html>
<html>
  <body>
  @@include(markdown('view.md'))
  @@include('./var.html', {
    "name": "haoxin",
    "age": 12345
  })
  </body>
</html>
```

view.md

```html
view
====
```

```js
var fileinclude = require('gulp-file-include'),
  markdown = require('markdown'),
  gulp = require('gulp');

gulp.task('fileinclude', function() {
  gulp.src(['index.html'])
    .pipe(fileinclude({
      filters: {
        markdown: markdown.parse
      }
    }))
    .pipe(gulp.dest('./'));
});
```

### `if` 语句

```js
fileinclude({
  context: {
    name: 'test'
  }
});
```

```
@@include('some.html', { "nav": true })

@@if (name === 'test' && nav === true) {
  @@include('test.html')
}
```

### `for` 语句

```js
fileinclude({
  context: {
    arr: ['test1', 'test2']
  }
});
```

```html
<ul>
@@for (var i = 0; i < arr.length; i++) {
  <li>`+arr[i]+`</li>
}
</ul>
```

### `loop` 语句

* index.html

```html
<body>
  @@loop('loop-article.html', [
    { "title": "My post title", "text": "<p>lorem ipsum...</p>" },
    { "title": "Another post", "text": "<p>lorem ipsum...</p>" },
    { "title": "One more post", "text": "<p>lorem ipsum...</p>" }
  ])
</body>
```

* loop-article.html

```html
<article>
  <h1>@@title</h1>
  @@text
</article>
```

### `loop` 语句 + data.json

data.json

```js
[
  { "title": "My post title", "text": "<p>lorem ipsum...</p>" },
  { "title": "Another post", "text": "<p>lorem ipsum...</p>" },
  { "title": "One more post", "text": "<p>lorem ipsum...</p>" }
]
```

* loop-article.html
  ```html
  <body>
  @@loop("loop-article.html", "data.json")
  </body>
  ```

### `webRoot` 内置的上下文变量

The `webRoot` field of the context contains the relative path from the source document to  
the source root \(unless the value is already set in the context options\).

### 示例

support/contact/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <link type=stylesheet src=@@webRoot/css/style.css>
  </head>
  <body>
    <h1>Support Contact Info</h1>
    <footer><a href=@@webRoot>Home</a></footer>
  </body>
  </body>
</html>
```

and the result is:

```html
<!DOCTYPE html>
<html>
  <head>
    <link type=stylesheet src=../../css/style.css>
  </head>
  <body>
    <h1>Support Contact Info</h1>
    <footer><a href=../..>Home</a></footer>
  </body>
  </body>
</html>
```



