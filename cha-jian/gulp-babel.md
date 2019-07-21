## 简介

Gulp 的 [Sass](https://www.sass.hk/) 插件，用于解析 `.sass` 文件。

[github 地址](https://github.com/dlmanning/gulp-sass)

## 安装

如果您想获得下一个版本的 `gulp-babel` 的预发行版，请安装 `gulp-babel`。

```
# Babel 7
$ npm install --save-dev gulp-babel @babel/core @babel/preset-env

# Babel 6
$ npm install --save-dev gulp-babel@7 babel-core babel-preset-env
```

## 用法

```js
const gulp = require('gulp');
const babel = require('gulp-babel');

gulp.task('default', () =>
    gulp.src('src/app.js')
        .pipe(babel({
            presets: ['@babel/preset-env']
        }))
        .pipe(gulp.dest('dist'))
);
```

## API

### babel\(\[options\]\)

#### options

See the Babel [options](http://babeljs.io/docs/en/options), except for `sourceMaps` and `filename` which is handled for you. Also, keep in mind that options will be loaded from [config files](http://babeljs.io/docs/en/config-files) that apply to each file.

## Source Maps

Use [gulp-sourcemaps](https://github.com/floridoo/gulp-sourcemaps) like this:

```js
const gulp = require('gulp');
const sourcemaps = require('gulp-sourcemaps');
const babel = require('gulp-babel');
const concat = require('gulp-concat');

gulp.task('default', () =>
    gulp.src('src/**/*.js')
        .pipe(sourcemaps.init())
        .pipe(babel({
            presets: ['@babel/preset-env']
        }))
        .pipe(concat('all.js'))
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('dist'))
);
```

## Babel Metadata

Files in the stream are annotated with a `babel` property, which contains the metadata from [`babel.transform()`](https://babeljs.io/docs/usage/api/).

#### Example

```js
const gulp = require('gulp');
const babel = require('gulp-babel');
const through = require('through2');

function logBabelMetadata() {
    return through.obj((file, enc, cb) => {
        console.log(file.babel.test); // 'metadata'
        cb(null, file);
    });
}

gulp.task('default', () =>
    gulp.src('src/**/*.js')
        .pipe(babel({
            // plugin that sets some metadata
            plugins: [{
                post(file) {
                    file.metadata.test = 'metadata';
                }
            }]
        }))
        .pipe(logBabelMetadata())
)
```

## Runtime

If you're attempting to use features such as generators, you'll need to add `transform-runtime` as a plugin, to include the Babel runtime. Otherwise, you'll receive the error: `regeneratorRuntime is not defined`.

Install the runtime:

```
$ npm install --save-dev @babel/plugin-transform-runtime
$ npm install --save @babel/runtime
```

Use it as plugin:

```js
const gulp = require('gulp');
const babel = require('gulp-babel');

gulp.task('default', () =>
    gulp.src('src/app.js')
        .pipe(babel({
            plugins: ['@babel/transform-runtime']
        }))
        .pipe(gulp.dest('dist'))
);
```



