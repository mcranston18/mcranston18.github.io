---
layout:     post
title:      Angular and SEO - What Didn't Work (Pt. I)
date:       2014-12-15
summary:    As of May 2014, Googlebot said it executes javascript but is it actually indexing single page apps?
comments:   true
categories: seo, angular
---

In May 2014, [Google announced its crawler would execute javascript](http://googlewebmastercentral.blogspot.ca/2014/05/understanding-web-pages-better.html). Previously, Googlebot and other crawlers could not execute javascript which means any single page app (regardless of framework) would not get indexed.

The solution was to have your server detect for crawlers, and then serve up a rendered version of your page. If your backend was written in NodeJS, you could somewhat painlessly use [PhantomJS](http://phantomjs.org/). If you didn't have a Node backend, it was a bigger pain. At [Flixel](https://flixel.com), we have a Django backend which means using the Django Casper module. However, our server instances didn't support using Node and Django on the same instance meaning we would have to set up another instance to even run this extra module.

The effort mentioned above is do-able but with Googlebot executing JS, we opted not to bother with a server side solution. Considering the opaque nature of indexing, we waited a while to see results.

We spent some time making the app more SEO-friendly (i.e. meta description, etc.) and still saw no results.

I e-mailed the author of [AngularJS & SEO - finally a piece of cake](https://weluse.de/blog/angularjs-seo-finally-a-piece-of-cake.html) to see what advice he had. His experience was similar to mine, after a month or two of waiting, no pages were being indexed. From the author:

> That's also the reason I haven't written my actually planned follow-up post to this point. When I wrote the piece you read, we hadn't any indexed, but a good amount of crawled pages. We've been waiting for a good month i published the write-up, trying to make some difference with the tools available in google webmaster tools; manually adding urls, recrawling, url-parameter stuff etc. At the end there hasn't been a single page in the index crawled by the 'js-feature'!

Perhaps things have changed since May, but it seems from two people's experience, relying on Googlebot to execute your javascript is not a good if SEO is a concern.
