---
layout:     post
title:      Maintainble Angular - Structuring Controllers
date:       2014-06-08 11:21:29
summary:    Proposal on separating code within Angular controllers.
comments:   true
---

*Your controllers should be thin.*  This is a common refrain when discussing maintainable Angular code. It is true but it can be more difficult to implement in practice. My proposal is to use simple comments to separate the lines of code in your controller:

1. Initialization values
  * assigning your variables and items on `$scope`
2. Private methods that are only used in the controller
3. Methods that are used in the view
4. CRUD
5. $watches
6. Getting data for the view
  * call a `get` request to populate the view with data on initialization
7. (hacks)
  * hacks happen. Inspired by [CSS Wizardy's shame.css](http://csswizardry.com/2013/04/shame-css/), labeling your hacks as such goes a long in maintainability.

Let's imagine a photo editing application, and the route at `/photo/123/edit`. This page would need to do the following:

1. Fetch the photo from the API
2. Allow posting to the API with updates to the photo
3. Allow the user to reset the form.
4. $watch something

The controller quickly becomes non-trivial. If we begin to need to add extra functionality (i.e. the ability to upload a watermark, the ability to offer extra editing features to premium users), then the controller could become even larger.

I would imagine the controller looking something like this:

{% highlight javascript %}

angular.module('app.photo')

.controller('EditPhotoCtrl', function($scope, PhotoService) {

  /*
   * Init Values
   */

  var ctrl = this;
  $scope.something = 'blue';
  $scope.isPremiumSubscriber = false;

  /*
   * Private Methods
   */

  ctrl._resizePhoto = function() { ... }
  
  /*
   * Methods Used in View
   */
  
  ctrl.showSomething = function() { ... }
  ctrl.resetForm = function() { ... }

  /*
   * CRUD
   */

  ctrl.getPhoto = {
    success: function(data) {
      $scope.photo = data;
    },

    error: function(error) {
      $scope.error = error;
    },

    invoke: function(photoId) {
      PhotoService.getPhoto(photoId)
        .then(this.success, this.error);
    }
  };
   
  ctrl.updatePhoto = {
    success: function(data) {
      $scope.photo = data;
      $scope.loading = false;
      $location.path('/photo/' + $scope.photo.id);
    },

    error: function(error) {
      $scope.loading = false;
    },
    
    createFormData: function(data) {
      // this func would create and return HTML5 formdata for multi-part upload
      return data;
    },

    invoke: function(photo, id) {
      $scope.loading = true;
      var formData = this.createFormData(photo);

      PhotoService.updatePhoto(formData, id)
        .then(this.success, this.error);
    }
  };
  
  /*
   * Get Data for View
   */
  
  ctrl.getPhoto.invoke();

  /*
   * Watches
   */
  
  $scope.$watch('something', function() { ... });

});


{% endhighlight %}
