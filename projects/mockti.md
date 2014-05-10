---
layout: page
title: "MockTi"
---
{% include JB/setup %}

MockTi is a mocking framework for Titanium. It uses the `api.jsca` to create
an entire fake `Ti` namespace. I made it during the course of writing the
Rutgers Mobile App; I wanted to test some new functionality that would've been
a pain to test without mocking all of Titanium, so I wrote MockTi.

The stubs generated also assert simple types such as numbers and strings.

More information available on the [GitHub page](https://github.com/rf/mockti).

### Example

{% highlight javascript %}
Ti = require('./mockti')();

var view = Ti.UI.createView();
var button = Ti.UI.createButton();

view.add(button);

button.addEventListener('click', function () {
  console.log('button clicked!');
});

button.fireEvent('click');
{% endhighlight %}
