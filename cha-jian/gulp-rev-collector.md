## 简介

Gulp 的插件，用于删除指定的文件或者文件夹。

[github 地址](https://github.com/peter-vilja/gulp-clean)

## 安装

```sh
$ npm install --save gulp-rev-collector
```

## 用法

We can use [gulp-rev](/cha-jian/gulp-rev.md) to cache-bust several assets and generate manifest files for them. Then using gulp-rev-collector we can collect data from several manifest files and replace links to assets in html templates.

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

gulp.task('rev', function () {
    return gulp.src(['rev/**/*.json', 'templates/**/*.html'])
        .pipe( revCollector({
            replaceReved: true,
            dirReplacements: {
                'css': '/dist/css',
                '/js/': '/dist/js/',
                'cdn/': function(manifest_value) {
                    return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
                }
            }
        }) )
        .pipe( minifyHTML({
                empty:true,
                spare:true
            }) )
        .pipe( gulp.dest('dist') );
});
```

### Options

#### collectedManifest

Type : `String`

It is a filename for collecded and merged manifest data in `json` format.

#### replaceReved

Type : `Boolean`

You set a flag, replaceReved, which will replace alredy replaced links in template's files. Default value is `false`.

#### dirReplacements

Type : `Object`

Specifies a directories replacement set. [gulp-rev](https://github.com/sindresorhus/gulp-rev) creates manifest files without any info about directories. E.c. if you use dirReplacements param from [Usage](#usage) example, you get next replacement:

```
"/css/style.css" => "/dist/css/style-1d87bebe.css"
"/js/script1.js" => "/dist/script1-61e0be79.js"
"cdn/image.gif"  => "//cdn8.example.dot/img/image-35c3af8134.gif"
```

#### revSuffix

Type : `String`

It is pattern for define reved files suffixes. Default value is '-\[0-9a-f\]{8,10}-?'. This is necessary in case of e.c. [gulp-rename](https://github.com/hparra/gulp-rename) usage. If reved filenames had different from default mask.

#### extMap

Type : `Object`

If You use some methods to modify the rev\_manifest.json after use [gulp-rev](https://github.com/sindresorhus/gulp-rev) and get a result like this:

```
{
    "assets/less/common.less": "assets/css/common-2c0d21e40c.css"
}
```

It means that keys and values in the rev\_manifest.json have different ext : less & css.

You can define extentions maping rules for correct manifest file processing.

Default value is:

```
{
    '.scss': '.css',
    '.less': '.css',
    '.jsx': '.js'
}
```

### Works with gulp-rev-collector

* [gulp-rev](https://github.com/sindresorhus/gulp-rev)



