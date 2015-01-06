---
layout:     post
title:      Angular $httpBackend Testing Gotchas
date:       2015-01-08
summary:    Account for all $http methods when unit testing
comments:   true
categories: angular, testing
---

[Click for the code of this post in a Plnkr](http://plnkr.co/edit/hcqXbMHaBUOSovopvNcV?p=info)

Imagine you have an Angular service:

{% highlight javascript %}
app.service('UserService', function($http) {
  return {
    getProfile: function() {
      return $http.get('profile.json');
    },

    getAccount: function() {
      return $http.get('account_details.json');
    }
  }
});
{% endhighlight %}

This is our main app config:

{% highlight javascript %}
app.config(function($stateProvider) {

    $stateProvider
    .state('main', {
      url: '/',
      templateUrl: 'template.html'
    });
});
{% endhighlight %}

Now, you want to write a simple unit test that ensures both methods on your service actually make a `GET` request.

{% highlight javascript %}
it('userService.getAccount should make a GET call', function() {
  userService.getAccount();
  httpBackend.expectGET('account_details.json').respond(200, 'a response');
  httpBackend.flush();
});

it('userService.getProfile should make a GET call', function() {
  userService.getProfile();
  httpBackend.expectGET('profile.json').respond(200, 'a response');
  httpBackend.flush();
});
{% endhighlight %}

This will fail. You will see the following:

{% highlight javascript %}
My Service userService.getAccount should make a GET call.
Error: Unexpected request: GET template.html Expected GET account_details.json
Error: Unexpected request: GET template.html
Expected GET account_details.json
{% endhighlight %}

This confused me. Why was $httpBackend trying to fetch the template on our main route?

The answer lies in the [Angular docs](https://docs.angularjs.org/api/ngMock/service/$httpBackend):

> Request expectations provide a way to make assertions about requests made by the application and to define responses for those requests. The test will fail if the expected requests are not made or they are made in the wrong order.

"Wrong order". Angular's $httpBackend service expected `template.html` as the first $http request, yet the unit test expects the `account_details.json` request to be the first.

![Are you kidding me?](/assets/images/are_you_kidding.jpg)

So this means, we need to satisfy the `GET` for `template.html` before we run our tests.

{% highlight javascript %}
beforeEach(inject(function(_$httpBackend_, UserService) {
  httpBackend = _$httpBackend_;
  httpBackend.whenGET(/.html*/).respond(200, '');
  httpBackend.flush();
}));
{% endhighlight %}

Here's the problem with the approach above. What if we have a resolve on our main app route as well?

{% highlight javascript %}
httpBackend.whenGET(/.html*/).respond(200, '');
httpBackend.whenGET('some_resolve_url').respond(200, '');
httpBackend.flush();
{% endhighlight %}

Do we really want to have this in every test file when we need to test $http?

My solution was to create a `helpers.js` file that lives in the testing folder. I create a global called `utils`:

{% highlight javascript %}
var utils = {
  appInitResolve: function(httpBackend) {
    httpBackend.whenGET(/.html*/).respond(200, '');
    httpBackend.whenGET('some_resolve_url').respond(200, '');
    httpBackend.flush();
  }
};
{% endhighlight %}

Now in each `beforeEach`, I call `utils.appInitResolve(httpBackend)`. Now if our resolve considerations change, we can update them in one file rather than crowd all the beforeEach blocks.
