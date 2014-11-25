---
layout:     post
title:      Gulp - Getting the return value from one task to another
date:       2014-11-21
summary:    Solution when a Gulp pipe needs to resolve syncronously with data from a preceeding pipe
comments:   true
categories: gulp
---

I am new to [GulpJS](http://gulpjs.com/) and was tasked with doing something fairly simple: append a git hash to index.html.

Getting the current git hash was easy enough:

{% highlight javascript %}
var git = require('gulp-git');
gulp.task('hash', function() {
  git.revParse({args:'--short HEAD'}, function (err, hash) {
    return hash;
  });)
});
{% endhighlight %}

What I struggled with with the Gulp-way of replace 'append git hash here!' with the actual git hash.

{% highlight javascript %}
var inject = require('inject-string');
gulp.task('html', function () {
  return gulp.src('app/index.html')
    .pipe(inject.append('append git hash here!'))
    .pipe(gulp.dest('dist'))
});
{% endhighlight %}

Running a function that would return the git hash would not work because the tasks are asyncronous.

My initial solution (which probably isn't the Gulp-way of doing it) was just to wrap the tasks ina  `q` promise.

{% highlight javascript %}
var git = require('gulp-git');
var inject = require('inject-string');
var q = require('q');

var getHash = function() {
  var deferred = q.defer();
  $.git.revParse({args:'--short HEAD'}, function (err, hash) {
    deferred.resolve(hash);
  });
  return deferred.promise;
};

gulp.task('html', function () {
  getHash().then(function(data) {
    return data;
  }).then(function(hash) {
    return gulp.src('app/index.html')
      .pipe(inject.append(hash))
      .pipe(gulp.dest('dist'))
  }
});
{% endhighlight %}

I was able to get some help from [Steve Lacy](https://github.com/stevelacy), the creator of several Gulp plugins. His solution just involves creating a global variable, and making the `html` task dependent on the `hash` (aka, `html` won't run until `hash` is finished).

{% highlight javascript %}
var gulp = require('gulp');
var git = require('gulp-git');
var inject = require('inject-string');

var gitHash;

gulp.task('hash', function(cb) {
  git.revParse({args:'--short HEAD'}, function (err, hash) {
    if (err) return cb(err);
    gitHash = hash;
    cb();
  });
});

gulp.task('html', ['hash'], function () {
  return gulp.src('app/index.html')
    .pipe(inject.append(gitHash))
    .pipe(gulp.dest('dist'));
});
{% endhighlight %}
