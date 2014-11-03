---
layout:     post
title:      SublimeText - Conditionally Removing Trailing Spaces
date:       2014-10-22
summary:    A fix so Sublime does not remove trailing white spaces in your templating engine.
comments:   true
categories: sublime text
---

Trailing white spaces are [annoying](http://programmers.stackexchange.com/questions/121555/why-is-trailing-whitespace-a-big-deal). Forunately, SublimeText automates this for us by simpling adding this options to our Sublime config:

{% highlight javascript %}
"trim_trailing_white_space_on_save": true
{% endhighlight %}

Except we are screwed if we use a templating language. Because whitespace will be trimmed in nested element which means you won't have a space between words. (Without littering your Jade with .)

Thanks to StackOverflow user [myk-willis](http://stackoverflow.com/questions/26510798/trailing-white-spaces-only-for-javascript-files-in-sublime-text/26512398#26512398), there's a solution:

1. Add `trim_trailing_white_space_on_save` to your Sublime user config (presuming you want this as your default).
2. Open a file-type where you do not want non-breaking spaces (i.e. Jade).
3. Go to `Preferences > Settings-More > Syntax Specific` and allow trailing spaces for that specific syntax:

{% highlight javascript %}
{
"trim_trailing_white_space_on_save": false
}
{% endhighlight %}

