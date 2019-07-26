## 去缓存

1. 生成  mainfest
2. 根据 mainfest 对 html 进行替换

```js
// sass
function sassTask() {
  return gulp.src('src/scss/**/*.scss')
    .pipe(rev()) // 位置很重重要
    .pipe(sass())
    .pipe(gulp.dest('dist/css')) 
    .pipe(rename({suffix: '.min'}))
    .pipe(cleanCSS())
    .pipe(gulp.dest('dist/css'))
    .pipe(rev.manifest()) // 生成 mainfest
    .pipe(rename({suffix: '.scss'}))
    .pipe(gulp.dest('dist/rev/'))
}

// css
function cssTask() {
  return gulp.src('src/css/**/*.css')
    .pipe(gulp.dest('dist/css'))
    .pipe(rev()) // 位置很重重要
    .pipe(rename({suffix: '.min'}))
    .pipe(cleanCSS())
    .pipe(gulp.dest('dist/css'))
    .pipe(rev.manifest()) // 生成 mainfest
    .pipe(rename({suffix: '.css'}))
    .pipe(gulp.dest('dist/rev/'))
}

// js
// script （拷贝 *.min.js，常规 js 则输出压缩与未压缩两个版本）
function scriptTask() {
  return gulp.src(['src/js/**/*.js'])
    .pipe(cached('script'))
    .pipe(rev())
    .pipe(gulp.dest('dist/js'))
    .pipe(filter(['**/*', '!**/*.min.js'])) // 筛选出管道中的非 *.min.js 文件
    // .pipe(jshint('.jshintrc')) // js的校验与合并，根据需要开启
    // .pipe(jshint.reporter('default'))
    // .pipe(concat('main.js'))
    // .pipe(gulp.dest('dist/js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('dist/js'))
    .pipe(rev.manifest())
    .pipe(rename({suffix: '.js'}))
    .pipe(gulp.dest('dist/rev/'))
}

// 图片
function imageTask() {
  return gulp.src('src/img/**/*.{jpg,jpeg,png,gif}')
    .pipe(cached('image'))
    .pipe(rev())
    .pipe(rename({suffix: '.min'})) // 对应 revCollector revSuffix 
    // .pipe(imagemin({optimizationLevel: 7, progressive: true, interlaced: true, multipass: true}))
    .pipe(imagemin([
      imagemin.jpegtran({progressive: true}),
    ], {optimizationLevel: 3}))
    // 取值范围：0-7（优化等级）,是否无损压缩jpg图片，是否隔行扫描gif进行渲染，是否多次优化svg直到完全优化
    .pipe(gulp.dest('dist/img'))
    .pipe(rev.manifest()) // 生成 mainfest
    .pipe(rename({suffix: '.img'}))
    .pipe(gulp.dest('dist/rev/'))
}
```

处理 html 文件：

```js
function htmlTask() {
    return gulp.src(['dist/rev/**/*.json', 'src/**/*.html'])
      .pipe( revCollector({
          replaceReved: true,
          revSuffix: '-[0-9a-f]{8,10}-?.min',
          dirReplacements: {
              'css': '/dist/css',
              '/js/': '/dist/js/',
              'cdn/': function(manifest_value) {
                  return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
              }
          }
      }))
      // 压缩
      // .pipe( minifyHTML({
      //     empty:true,
      //     spare:true
      // }) )
      .pipe( gulp.dest('dist/pages') );
}
```

结果：

```js
<!doctype html>
<html>
<head>
<meta charset="UTF-8">
<title>Untitled Document</title>
<link rel="stylesheet" href="/dist/css/main-351bdd8531.min.css">
<link rel="stylesheet" href="/dist/css/app-333e486def.min.css">
</head>

<body>
   <p class="app">哈哈哈</p>
   <p class="main">嘿嘿</p>
</body>
</html>
```



