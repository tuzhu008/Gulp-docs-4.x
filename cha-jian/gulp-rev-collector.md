## 简介

Gulp 的插件，用于删除指定的文件或者文件夹。

[github 地址](https://github.com/peter-vilja/gulp-clean)

## 安装

```sh
$ npm install --save gulp-rev-collector
```

## 用法

我们可以使用 [gulp-rev](/cha-jian/gulp-rev.md) 来 cache-bust  一些资源，并为它们生成清单文件。然后使用 `gulp-rev-collector`，我们可以从几个清单文件收集数据，并替换到 html 模板中的资源的链接。

```js
var gulp = require('gulp');
var rev = require('gulp-rev');

gulp.task('css', function () {
    return gulp.src('src/css/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist/css'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/css' ) );
});

gulp.task('scripts', function () {
    return gulp.src('src/js/*.js')
        .pipe(rev())
        .pipe(gulp.dest('dist/js'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/js' ) );
});
```

```js
var revCollector = require('gulp-rev-collector');
var minifyHTML   = require('gulp-minify-html');

function revTask() {
  return gulp.src(['rev/**/*.json', 'src/pages/**/*.html'])
    .pipe(revCollector({
      replaceReved: true,
      dirReplacements: {
        'css': '/dist/css',
        '/js/': '/dist/js/',
        'cdn/': function(manifest_value) {
          return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
        }
    }))
    .pipe(gulp.dest('dist'));
}
```

### 选项

#### collectedManifest

Type : `String`

它是一个 `json` 格式的用于收集和合并清单数据的文件名。

#### replaceReved

Type : `Boolean`

设置一个 `replaceReved`  标志，它将替换模板文件中的准备被替换的链接。默认值为 `false`。

#### dirReplacements

Type : `Object`

指定目录替换集。[gulp-rev](/cha-jian/gulp-rev.md) 创建不包含任何目录信息的清单文件。如果你使用 `dirreplacement` 参数的例子，将得到如下替换:

```
"/css/style.css" => "/dist/css/style-1d87bebe.css"
"/js/script1.js" => "/dist/script1-61e0be79.js"
"cdn/image.gif"  => "//cdn8.example.dot/img/image-35c3af8134.gif"
```

#### revSuffix

Type : `String`

它是定义 reved 文件后缀的模式。默认值是 '-\[0-9a-f\]{8,10}-?'。在使用 [gulp-rename](/cha-jian/gulp-rename.md) 时，这是必要的。

如果重新生成的文件名与默认掩码不同，`revSuffix` 是文件名和带点的文件扩展中间的部分。

`revSuffix` 参数会被初始化为一个正则表达式， 而是否进行替换是使用这个正则将 mainfest 里面的匹配的值替换为空字符后剩余部分与 mainfest 的键的数组做比对来判断的。如：

先看源码:

```js
var cleanReplacement =  path.basename(json[key]).replace(new RegExp( opts.revSuffix ), '' );
```

示例：

```js
// mainfest.json 
{
  "main.css": "main-351bdd8531.min.css"
}
```

默认替换后的字符串：`main.min.css`，它与 key 没有匹配的，因此不会替换。

要解决这个问题需要传入 `revSuffix` 参数，从源码处可以看到，这个参数直接被使用了，因此，如果需要替换上面的文件名，就需要传入 `-[0-9a-f]{8,10}-?.min` ，而不是只传入 `.min` 或 `min`

#### extMap

Type : `Object`

如果你使用一些方法修改 rev\_manifest.json 后，使用 [gulp-rev](/cha-jian/gulp-rev.md) ，得到这样的结果:

```
{
    "assets/less/common.less": "assets/css/common-2c0d21e40c.css"
}
```

这意味着 rev\_manifest.json 中的键和值有不同的 ext: less 和 css。

您可以定义扩展映射规则，以正确地处理清单文件。

默认值是:

```
{
    '.scss': '.css',
    '.less': '.css',
    '.jsx': '.js'
}
```

### Works with gulp-rev-collector

* [gulp-rev](https://github.com/sindresorhus/gulp-rev)



