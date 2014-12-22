---
layout:     post
title:      Angular and SEO - What Didn't Work (Pt. 2)
date:       2014-12-22
summary:    Use your web server to detect crawler and serve up static pages.
comments:   true
categories: seo, angular
---

After [realizing that Googlebot was not actually indexing our single page app]({% post_url 2014-12-15-angular-seo-pt1 %}), we decided to try to whip up a very quick server side solution:

1. use [grunt-html-snapshot](https://github.com/cburgdorf/grunt-html-snapshot) to generate static pages that are put in a `/static/snapshots/` folder in django
2. nginx detects for a crawler,
3. if detected, maintain the same file path, but preprend with the `/static/snapshots/` route
  - i.e. example.com/about -> example.com/static/snapshots/about

This was a bad solution for a variety of reasons:

1. It's not dynamic. If the crawler hits a user profile or a dynamic page, we would not serve up the correct content.
2. Google doesn't like it when you serve the crawler something different from your sites real content.
3. We were still sending a [soft 404](https://support.google.com/webmasters/answer/181708).
4. We would have to generate new snapshots on each deploy and add/substract/edit new routes anytime something changed.
5. Even in their [official docs](https://developers.google.com/webmasters/ajax-crawling/docs/faq#redirects), Google doesn't sound all that thrilled about using redirects for SEO.

To recap, attempts at SEO:

<del>1. Relying on Googelbot executing JS</del>

<del>2. Using a 302 redirect.</del>
