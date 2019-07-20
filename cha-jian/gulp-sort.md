## 简介

Gulp 插件，用于按路径或任何自定义排序比较器对流中的文件进行排序。

[github 地址](https://github.com/pgilad/gulp-sort)

## 安装

```
npm install --save-dev gulp-sort
```

## 用法

```js
var sort = require('gulp-sort');

// default sort
gulp.src('./src/js/**/*.js')
  .pipe(sort())
  .pipe(gulp.dest('./build/js'));

// pass in a custom comparator function
gulp.src('./src/js/**/*.js')
  .pipe(sort(customComparator))
  .pipe(gulp.dest('./build/js'));

// sort descending
gulp.src('./src/js/**/*.js')
  .pipe(sort({
       asc: false
  }))
  .pipe(gulp.dest('./build/js'));

// sort with a custom comparator
gulp.src('./src/js/**/*.js')
  .pipe(sort({
      comparator: function(file1, file2) {
          if (file1.path.indexOf('build') > -1) {
              return 1;
          }
          if (file2.path.indexOf('build') > -1) {
              return -1;
          }
          return 0;
      }
  }))
  .pipe(gulp.dest('./build/js'));

// sort with a custom sort function
var stable = require('stable');
gulp.src('./src/js/**/*.js')
  .pipe(sort({
      customSortFn: function(files, comparator) {
          return stable(files, comparator);
      }
  }))
  .pipe(gulp.dest('./build/js'));
```

## 选项

`gulp-sort` 接受一个可选的比较器函数，或带有以下参数的 dictionary：

### asc

升序。默认值为 `true`。指定 `false` 按降序排序。

### comparator

要使用的比较器函数。`comparator(file1, file2)`。默认为文件路径的 `localeCompare`。

### customSortFn

使用 `customSortFn` 来自己控制排序\(对于稳定的排序非常有用\)。

`customSortFn`签名如下：

`customSortFn(<files>, <comparator>)`

* `files`
  是传入的 vinyl 文件对象

* `comparator`
  使用默认的比较器，或者作为参数传递的自定义比较器。

这个函数将返回已排序的文件列表。



