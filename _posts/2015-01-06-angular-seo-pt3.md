---
layout:     post
title:      Angular and SEO - What Worked (Pt. 3)
date:       2015-01-07
summary:    Just use prerender.io
comments:   true
categories: seo, angular
---

After [two]({% post_url 2014-12-15-angular-seo-pt1 %}) [failed]({% post_url 2014-12-22-angular-seo-pt2 %}) attempts, we decided to use [prerender.io](https://prerender.io/). They offer two versions:

1. Paid (up to 20,000 cached pages for $15/month)
2. Use their free open-source version. You will want to handle caching yourself.

We went with the paid vesion to see if we would see a quick turn around on indexing.

First off, it took about four lines of code:

{% highlight javascript %}
var prerender = require('prerender-node');
prerender.set('protocol', 'https');
prerender.set('host', '<host>');
prerender.set('prerenderToken', '<token>');
{% endhighlight %}

Secondly, we saw our entire site indexed by Google within about a week. Prerender worked easily and instantly.

Lastly, Todd Hopper, the lead engineer, really goes above and beyond to help with any issues. (In our case, a buffering issue with nginx that was causing the page to fail to completely render.) For anyone considering a service (paid or free) to help SEO for a single-page app, I have only good things to say about Prerender.
