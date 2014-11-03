---
layout:     post
title:      Gulp - Fix to socket hang up using httpProxy
date:       2014-11-03
summary:    Fix to Gulp crashing when using httpProxy
comments:   true
categories: gulp
---

If you use the [Yeoman](http://yeoman.io) Gulp generator, you might be getting an incredibly frustrating socket hang us anytime an HTTP request fails or is interrupted.

{% highlight javascript %}
Error: socket hang up
    at createHangUpError (http.js:1472:15)
    at CleartextStream.socketCloseListener (http.js:1522:23)
    at CleartextStream.emit (events.js:95:17)
    at tls.js:693:10
    at process._tickCallback (node.js:419:13)
{% endhighlight %}

Adding simple error handling to your httpProxy will disallow the hangup and keep your sanity in check:

{% highlight javascript %}
var proxy = httpProxy.createProxyServer({
  target: proxyTarget
}).on('error', function(e) {
  console.log(JSON.stringify(e, null, ' '))
});
{% endhighlight %}
