---
layout:     post
title:      $onChanges & Deep Watches
date:       2016-11-04
summary:    Understanding the limitations of $onChanges
comments:   true
hidden:     true
categories: angular, components
---

## TL:DR

`$onChanges` will not fire just because a bindable object changes. If you need to know every time the object changes, you will have to pass in a new/copied version of the object, or use `$doCheck`. [Here is a live example on plnkr](http://plnkr.co/edit/rJZdWgOhwqSkz3spg3TS?p=preview).

## The Problem

Image we have a data object that gets passed into a child component:
{% highlight javascript %}
$ctrl.data = {color: blue};
{% endhighlight %}

{% highlight javascript %}
angular
  .module('app')
  .component('child', {
    bindings: {
      data: '<'
    },
    controller: class {
      $onChanges(changes) {
        // this method will only fire if this.data is a new object
      }
    }
  });
{% endhighlight %}

Whenever the `color` property changes, we want to run a funtion. The problem is that the `$onChanges` event will not fire because the object itself has not changed.

## Three Possible Solutions

1.
In your parent, pass in a copied version of `this.data` every time by using `angular.copy(this.data)`.

2.
Add the `$doCheck` hook to your component which will fire anytime the scope of your component changes. The downside of this approach is that it could be a performance hit.

{% highlight javascript %}
angular
 .module('app')
 .component('child', {
   bindings: {
     data: '<'
   },
   controller: class {
     $doCheck(changes) {
       // this method will fire anytime the component's scope changes
     }
   }
 });
{% endhighlight %}

3.
You can still technically add `$scope.$watch` to your $onInit method, but this is a bad solution. `$scope.$watch` is not apart of Angular 2 and relying on it is counter-productive to component-based architecture and any upgrade strategy.

 {% highlight javascript %}
 angular
   .module('app')
   .component('child', {
     bindings: {
       data: '<'
     },
     controller: class {
       constructor($scope) {
         this.$scope = $scope;
       }

       $onInit() {
         $scope.$watch('$ctrl.data', function(value) {
           // do something
         }, true);
       }
     }
   });
 {% endhighlight %}


## Why $onChanges does not do deep watches

This is [by design](https://github.com/angular/angular.js/issues/14378#issuecomment-207351242)

> This is indeed intended. It would be too expensive to check each object deeply for changes. Besides, in most cases, where bindings are used in a template, an "internal" change will automatically update the view.If you want to account for "deep" changes, you need to manually $watch the object.

## Further Reading

- [Todd Motto's posts and guides](https://github.com/toddmotto/angular-styleguide)
- [triggering $onChanges for updated one way binding #14378](https://github.com/angular/angular.js/issues/14378#issuecomment-207351242)
- [Component Lifecycle: $doCheck (angular 1.5.x)](http://www.kristofdegrave.be/2016/07/component-lifecycle-docheck-angular-15x_22.html)

