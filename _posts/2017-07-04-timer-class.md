---
layout:     post
title:      A Timer class to profile methods in Node JS
date:       2017-07-04
summary:    Using a simple Timer class to figure out how long a method takes to finish.
comments:   true
categories: node js, profiling, performance
---

A simple node `Timer` class so you can easily tell how long it takes for a method to complete:

{% highlight javascript %}
// timer.js
function Timer() {
  this.startTime = [];
}

Timer.prototype.start = function() {
  console.log('Starting timer ...');
  this.startTime = process.hrtime();
};

Timer.prototype.finish = function() {
  const hrtime = process.hrtime(this.startTime);
  const seconds = (hrtime[0] + hrtime[1] / 1e9).toFixed(3);
  console.log('Finished in ' + seconds + 's');
};

module.exports = Timer;
{% endhighlight %}

Usage:

{% highlight javascript %}
// app.js
const Timer = require('./Timer');

function myMethod() {
  const timer = new Timer();
  timer.start();

  setTimeout(function() {
    timer.finish(); // logs "Finished in 3.506s"
  }, 3500);
}

myMethod();
{% endhighlight %}
