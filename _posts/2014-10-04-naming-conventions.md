---
layout:     post
title:      Naming Conventions and Folder Structure in an AngularJS App
date:       2014-10-03
summary:    Proposal for naming files and folder structure in a large Angular app.
comments:   true
categories: angularjs naming convetions best practices code organization
---

It's considered [best practice](https://docs.google.com/document/d/1XXMvReO8-Awi1EZXAXS4PzDzdNvV6pGcuaF4Q9821Es/mobilebasic?pli=1) to organize your Angular app as modules, rather than directories. 

With help from a co-worker [Marc Gibbons](https://github.com/marcgibbons), we used the 

* Inside of a module, there should be an `index.html` and a `module.js` file.
* Controllers are append with `.ctrl.js`, i.e. `photos.ctrl.js`
* Services are appended with `.services.js`, i.e. `photos.service.js`
* Directive are appended with `.directives.js`, i.e. `photo-list.directive.js`
* File names are separated with a hyphen, not camelcase.
* All Javascript uses camelcase

If we imagine a structure for 500px's [Flow](https://500px.com/flow) and [Photo](https://500px.com/photo/85027657/flower-queen-by-darya-chacheva?from=user) views, it might look something like:[^1]

{% highlight javascript %}

500px/

  app/

    flow/
      index.jade
      module.js
      flow.ctrl.js
      flow-modal.ctrl.js
      flow.service.js
      modal-view.jade

    photo/
      index.jade
      module.js
      photo.ctrl.js
      photo-detail.jade
      photo-edit.jade
      photo-edit.ctrl.js
      photo.service.jss
  
  common/

    directives/
      modal/
        index.jade
        module.js

{% endhighlight %}

Notes:

* I would keep modal as its own abstract directive. It would be up to the view to populate it.
* They use a masonry plugin for the `/focus` route, so I would likely create something like a `flow-list.directive.js` that would handle grabbing a list of photos, and arranging them.

Further reading:

- [Building Huuuuuge Apps with AngularJS](http://briantford.com/blog/huuuuuge-angular-apps)
- [Code Organization in Large AngularJS and JavaScript Applications](http://cliffmeyers.com/blog/2013/4/21/code-organization-angularjs-javascript)
- [Code organization in angularjs](http://tech-blog.maddyzone.com/javascript/code-organization-angularjs)



[^1]: This is just a crude example of how I might imagine they would structure two modules.
