---
layout: post
title:  Composition in JavaScript
date:   2016-06-18 17:00:12 +0000
permalink: /coding/composition-in-javascript
published: false
category: coding
meta_description: >
 Using composition instead of classical inheritance in JavaScript
---

JavaScrit is very expressive language and I one of the main reasons I enjoy using it. An amazing feature is the ability to create and inherit from objects without classes and class inheritance. Using compositional tactics we can piece together multiple objects to form new ones.

One of the ways is _Mixins_. This is essentially coping properties and methods from one object to another. It can be achieved by using ES6 `Object.assign()` function which copies one or more objects into one. Lodash extend achieves the same thing if you need older browser support.

Below is an example of creating mixing:

{% highlight javascript %}
{% endhighlight %}

The next tactic is to use _functional inheritance_ similar to _Mixins_ but you can use closures to enforce private state.