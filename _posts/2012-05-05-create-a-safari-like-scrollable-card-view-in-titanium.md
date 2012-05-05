---
layout: post
title: "Create a Safari like Scrollable Card View in Titanium"
category: 
tags: []
---
{% include JB/setup %}

Here's the end result:

<img src="{{ ASSET_PATH }}/../../cardview.png" />

This isn't too hard -- It's just a ScrollableView.  But, the view is
actually thinner than the screen. It has the property `clipViews` set to
`false`, so even though the child views are outside of the bounds of the
ScrollableView, they're still visible.  We use the `hitRect` property to
ensure that touches on the views outside of the bounds of the `ScrollableView`
will still scroll the view.  I recently contributed this property to Titanium,
so you'll have to use a recent CI build of 2.1, if 2.1 hasn't been released yet.

Let's have a look at the code.

{% highlight javascript %}
var win = Ti.UI.createWindow();

var view1 = Ti.UI.createView({backgroundColor: '#123', width: 220, height: 300});
var view2 = Ti.UI.createView({backgroundColor: '#246', width: 220, height: 300});
var view3 = Ti.UI.createView({backgroundColor: '#48b', width: 220, height: 300});

var scrollableView = Ti.UI.createScrollableView({ 
  views: [view1,view2,view3],
  showPagingControl: true,
  width: 230, 
  height: 430,
  clipViews: false, 
  hitRect: {  
    height: 480,
    width: 320,
    x: -45,
    y: 0     
  }
});

win.add(scrollableView);                                                           
win.open();    
{% endhighlight %}

When touches are received against the view, they are checked against the
`hitRect`.  These coordinates will be local to the `ScrollableView` itself.
So, we set the hit rectangle to be as wide as the screen, but offset 45px back.
45px is half of the difference between the width of the screen and the width
of the scrollable view: `(320 - 230) / 2`.
The `hitRect`, visualized, looks like the larger blue box here:

<img src="{{ ASSET_PATH }}/../../cardview-box.png" />

The inner pink box represents the bounds of the `ScrollableView`.
So, even though some touch events are actually outside of the `ScrollableView`,
they will still scroll the view.  This way, those 'previews' on the sides of
the screen can be grabbed and scrolled over by the user.

To give credit where it's due, I learned how to do this from 
[this blog post.](http://blog.proculo.de/archives/180-Paging-enabled-UIScrollView-With-Previews.html)
