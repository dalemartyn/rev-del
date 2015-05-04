# rev-del [![Build Status](https://travis-ci.org/callumacrae/rev-del.svg)](https://travis-ci.org/callumacrae/rev-del)

Delete old, unused fingerprinted files from a revision manifest generated by a
module such as [gulp-rev].

## Install

```
$ npm install --save-dev rev-del
```

## Usage

```js
revDel({
	oldManifest: 'rev-manifest.json',
	newManifest: { /* a manifest */ },
	suppress: true
}, function (err, filesDeleted) {
	// ...
});
```

The manifests can be either objects or paths to files. `oldManifest` defaults
to "rev-manifest.json" (which is the default output for gulp-rev), and
newManifest has no default; it's assumed that you've got it in memory, so it's
more efficient to pass it in as an object.

`suppress`, when set to false, will throw an error when rev-manifest.json
doesn't exist. You don't normally want to change: usually, the first time you
run your revisioning script, the manifest doesn't exist yet.

### With Gulp

rev-del works great with Gulp and gulp-rev. You need to specify either `dest`
in the options, or `base` in the manifest options—unless you're writing
everything to the root directory.

```js
var gulp = require('gulp');
var rev = require('gulp-rev');
var revDel = require('rev-del');

gulp.task('default', function () {
    return gulp.src('src/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist'))
        .pipe(rev.manifest())
        .pipe(revDel({ dest: 'dist' }))           // <--
        .pipe(gulp.dest('dist'));
});
```

You can pass it the name of the old manifest if you're not using the default:

```js
var gulp = require('gulp');
var rev = require('gulp-rev');
var revDel = require('rev-del');

gulp.task('default', function () {
    return gulp.src('src/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist'))
        .pipe(rev.manifest('my-manifest.json'))
        .pipe(revDel('some-other-manifest.json'))        // <--
        .pipe(gulp.dest('dist'));
});
```

Call it **before** you call `gulp.dest`.

Note that you only need to change the name of manifest if it is different to
the path given to `rev.manifest()`: if you just change the name of the
manifest, rev-del will handle it fine:

```js
// This works
var gulp = require('gulp');
var rev = require('gulp-rev');
var revDel = require('rev-del');

gulp.task('default', function () {
    return gulp.src('src/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist'))
        .pipe(rev.manifest('my-manifest.json'))
        .pipe(revDel({ dest: 'dist' }))                          // <--
        .pipe(gulp.dest('dist'));
});
```

You can also specify the `base` as one of the options. Again, you don't need to
do this if you've given it to `rev.manifest()`!

You can also add `force: true` as an option. This is passed through to the underlying [del](https://github.com/sindresorhus/del#optionsforce) plugin which accepts it to allow deletion outside the current working directory. This comes in handy when you get a gulp error like 'Cannot delete files/folders outside the current working directory. Can be overriden with the `force` option.'

## License

Released under the MIT license.


[gulp-rev]: https://github.com/sindresorhus/gulp-rev
