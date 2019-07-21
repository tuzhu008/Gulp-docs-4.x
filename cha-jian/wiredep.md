## 简介

将 [Bower](http://bower.io/) 依赖项连接到源代码，它可以使用流工作，可以与 gulp 无缝集成。

[github 地址](https://github.com/taptapship/wiredep)

## 安装

```
npm install --save-dev wiredep
```

## 用法

使用 bower 安装依赖项\(如果你还没有安装\):

```
$ bower install --save jquery
```

在代码中插入占位符，依赖将被注入：

```html
<html>
<head>
  <!-- bower:css -->
  <!-- endbower -->
</head>
<body>
  <!-- bower:js -->
  <!-- endbower -->
</body>
</html>
```

添加 Gulp 任务：

```js
var wiredep = require('wiredep').stream;

function bowerTask() {
  return gulp.src('./src/footer.html')
    .pipe(wiredep({
      optional: 'configuration',
      goes: 'here'
    }))
    .pipe(gulp.dest('./dest'));
}
```

运行任务后：

```html
<html>
<head>
  <!-- bower:css -->
  <!-- endbower -->
</head>
<body>
  <!-- bower:js -->
  <script src="bower_components/jquery/dist/jquery.js"></script>
  <!-- endbower -->
</body>
</html>
```



