---
layout: post
title:  "Greetings, Jonas!"
date:   2014-08-18 4:31:00pm
categories: jekyll update javascript
image: http://wallet.kaazing.com/landing/resources/images/demos.png
---

Hey Jonas,

This thing rocks...

{% highlight javascript %}
/*!
 * GMaps.js v0.2.30
 * http://hpneo.github.com/gmaps/
 *
 * Copyright 2012, Gustavo Leon
 * Released under the MIT License.
 */

if(window.google && window.google.maps){

  var GMaps = (function(global) {
    "use strict";

    var doc = document;
    var getElementById = function(id, context) {
      var ele
      if('jQuery' in global && context){
        ele = $("#"+id.replace('#', ''), context)[0]
      }else{
        ele = doc.getElementById(id.replace('#', ''));
      };
      return ele;
    };
{% endhighlight %}

Enjoy the rest of your day!

[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll]:    http://jekyllrb.com
