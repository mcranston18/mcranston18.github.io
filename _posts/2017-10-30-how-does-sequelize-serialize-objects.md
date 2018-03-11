---
layout:     post
title:      How Sequelize Serializes Objects
date:       2017-07-04
summary:    How Sequelize class instances get serialized in Express
comments:   true
categories: node js, sequelize
---

When retrieiving data from Sequelize, you don't get a javascript object representing your data, [you get a javascript object representing a model instance](http://docs.sequelizejs.com/manual/tutorial/models-usage.html#data-retrieval-finders).


<iframe src="https://giphy.com/embed/3osxY4r5ejQcR123eM" width="480" height="270" frameBorder="0"></iframe>


This confused me. So I began tracing how a Sequelize model instance becomes JSON for a front end to consume. Here are the steps:

1.
Sequelize retrieves a record from the database and returns a javascript object (i.e. a model instance) representing that data.

{% highlight javascript %}
const movie = await Movie.findById('1234');
console.log(movie);
// {
//   dataValues:{
//     name: 'Ladybird',
//     created_at: 2016-02-08T19:46:08.303Z
//   },
//   _changed: {},
//   _modelOptions: {},
//   _options: {}},
//   __eagerlyLoadedAssociations: [],
//   isNewRecord: false,
//   toJSON: function() {
//     // return JSON representation of instance
//   }
// }
{% endhighlight %}

2.
ExpressJS sends the model instance back with `res.send`:

{% highlight javascript %}
const movie = await Movie.findById('1234');
res.status(200).send(movie);
{% endhighlight %}

By calling `.send`, Express starts serialization:

- The `.send` method calls `JSON.stringify(instance)` with Sequelize model instance [(Reference)](https://github.com/expressjs/express/blob/master/lib/response.js#L1114-L1134)
- `JSON.stringify()` then checks to see if the object being stringified has a funtion named `toJSON`. If it does, it will stringify the return value of that function. If not, it will stringify the plain object being passed. [(Reference)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON_behavior)
- Since each Sequelize instance has a `toJSON` method, the final object being returned to the user is the result of that method [(Reference)](https://github.com/sequelize/sequelize/blob/master/lib/model.js#L4100-L4106)

This means if you ever want to add custom properties to your return value, you will need to add those values to a raw javascript object:
{% highlight javascript %}
const instance = await Movie.findById('1234');
const movie = instance.get(); // {name: 'Ladybird'}
movie.extraProperty = '1234';
res.status(200).send(movie);
{% endhighlight %}
