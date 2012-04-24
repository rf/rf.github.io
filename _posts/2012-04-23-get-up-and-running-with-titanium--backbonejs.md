---
layout: post
title: "Get Up and Running with Titanium and Backbone.js"
category: 
tags: []
---
{% include JB/setup %}

The following assumes you have the iPhone SDK installed and Xcode properly
configured on your OS X computer.  In the future I may update this post to be
compatible with Android and or Linux

So, apparently, you want to try Appcelerator Titanium.  I'm not really sure
why you want to do this, but this tutorial will get you started.

I wrote a tool to manage Titanium SDK versions. This tool requires node.js.
If you don't already have node installed, you can just download it from
[nodejs.org](http://nodejs.org) and run

{% highlight bash %}
$ ./configure
$ make
$ sudo make install
{% endhighlight %}

Once you have node installed, you'll have to install my tool.  It's called `tsm`
and it allows you to download and install Titanium SDK versions easily. Install
it with npm like so:

{% highlight bash %}
$ sudo npm -g install tsm
{% endhighlight %}

The `-g` option to `npm` (the node package manager) installs the package
globally.  You'll need to do this as the `tsm` bin (or a symlink to it) must be
put in your `PATH` by `npm`.

Now, you can run it like so:

{% highlight bash %}
$ tsm ls
{% endhighlight %}

This command will hit the Appcelerator build server and check to see what sdk
versions are available to be installed.  You'll need to install the latest
available stable version.  At the time of writing, this is 2.0.1.  Take a look
at the [Appcelerator docs site](http://docs.appcelerator.com/titanium/2.0/index.html)
and see what version is right underneath **SDK Updates**.  This is the version
you want.  Install it like so:

{% highlight bash %}
$ tsm install 2.0.1
{% endhighlight %}

You'll see a progressbar tick across as your SDK is downloaded and unzipped for
you.  When it's done, you're ready to rock.  Download my `backbone.js` and
Titanium template with git:

{% highlight bash %}
$ git clone https://github.com/russfrank/backbone.ti myproject
$ git submodule init
$ git submodule update
{% endhighlight %}

Now, try running the project:

{% highlight bash %}
$ cd myproject
$ tsm run 2 run --platform=iphone
{% endhighlight %}

and the sample `backbone.js` app should pop up in the simulator.

## What did I just do?

Well, first, you used `tsm` to install an SDK version.  Then, you cloned the
repository where I keep a `backbone.js` skeleton project.  You had to
initialize `git-submodule` because `underscore.js` and `backbone.js` were
submoduled out.  Then, you used `tsm run` to run the `titanium.py` script that
came with a particular version of Titanium.

`tsm run` takes one argument; the rest of the arguments are passed straight to
the [Titanium command line tool](https://wiki.appcelerator.org/display/guides/Titanium+Command+Line+Interface).
The one argument it takes is a version selector.  You selected version 2, which
probably matched the sdk version you grabbed above.

## Moving Forward

Eventually I will write some tutorials about how to best use **Backbone.js**
with Titanium.  For now, I'd recommend setting up a Backbone.View with just
what's needed for display; any logic that will work on any platform should not
go in the View, but a Controller class.  This way, you can write a View for
each platform and just include the one you need for a particular platform.

You can also create a blank project with

{% highlight bash %}
$ tsm run 2 create --platform=iphone,ipad,android --type=project --name=myapp --id=com.my.app
{% endhighlight %}

if you do not wish to use Backbone.js at all.
