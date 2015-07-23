---
layout:     post
title:      The Stupid Reason Why PhantomJS Isn't Running On Your Heroku Project
date:       2015-05-07
summary:    It's the buildpacks.
comments:   true
categories: phantomjs, heroku, nodejs
---

If you want to run PhantomJS on a NodeJS Heroku project, you may have seen the dreaded error:

{% highlight javascript %}
phantomjs-node: You don't have 'phantomjs' installed
{% endhighlight %}

Apparently, all I had to do was ensure my PhantomJS buildpack came *before* the NodeJS buildpack. Using the command line, run:

{% highlight javascript %}
heroku buildpacks:add --index 1 https://github.com/stomita/heroku-buildpack-phantomjs.git
heroku buildpacks:add https://github.com/heroku/heroku-buildpack-nodejs
{% endhighlight %}


The `--index 1` flag puts the PhantomJS buildpack in the first position. The last buildpack in the list is the one that Heroku uses to boot your app. Any other buildpacks that your app eventually rely on must go first in that list.
