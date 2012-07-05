---
layout: post
title: "How to Not Use Titanium Studio"
category: 
tags: []
---
{% include JB/setup %}

This is a tutorial for those who are frightened by the bloat of Titanium Studio
and would rather use the python scripts directly.  It would help if [you use my
tool, `tsm`, to manage your Titanium SDK versions](https://github.com/russfrank/tsm);
however, if you'd rather not, replace the bottom references to

- `tsm run 2.0.1` with `/path/to/mobilesdk/os/2.0.1/titanium.py`
- `tsm builder.py 2.0.1 iphone` with `/path/to/mobilesdk/os/2.0.1/iphone/builder.py`
- `tsm builder.py 2.0.1 android` with `/path/to/mobilesdk/os/2.0.1/android/builder.py`

You can install `tsm` like this:

{% highlight bash %}
$ npm i -g tsm
{% endhighlight %}

If you're using `tsm` -- awesome! Grab the latest stable sdk with

{% highlight bash %}
$ tsm install 2.0.1
{% endhighlight %}

and you're ready to rock.  Most of what you'll need to do can be accomplished
with the `titanium.py` script, which you can run with the `tsm run` command.
However, we'll have to use the `builder.py` script directly for some things
which are either not possible or broken with the `titanium.py` script.  Here's
the basics:

## iPhone Simulator

{% highlight bash %}
$ tsm run 2.0.1 run --platform=iphone
{% endhighlight %}

## iPad Simulator

This one uses the builder.py script.  You'll have to replace
$IOS_VER with the version of simulator to use (such as 4.0), $IOS_ID with your
app id, and $APP_NAME with the name of the app.

{% highlight bash %}
$ tsm builder 2.0.1 iphone run "`pwd`" $IOS_VER $IOS_ID $APP_NAME ipad
{% endhighlight %}

So for example:

{% highlight bash %}
$ tsm builder 2.0.1 iphone run "`pwd`" 5.1 com.test.app TestApp ipad
{% endhighlight %}

## iOS Distribution

For distribution, we need the `builder.py` script.

{% highlight bash %}
$ tsm builder 2.0.1 iphone distribute $IOS_VER "`pwd`" $IOS_ID $APP_NAME $MOBILEPROVISION $DEVIDENT "`pwd`/build/iphone/" universal
{% endhighlight %}

This will create an $APP_NAME.app which you can codesign with `/usr/bin/xcrun`;
I described this in a [previous post](http://russfrank.us/2012/03/11/automatic-enterprise-distribution-in-titanium/).

## Android Emulator

To start the Android emulator, we need the Android `builder.py` script

{% highlight bash %}
$ tsm builder 2.0.1 android emulator $APP_NAME $ANDROID_SDK "`pwd`" $ANDROID_ID "6" $ANDROID_SKIN
{% endhighlight %}

"6" is the AVD ID, which identifies this Android device.  You'll see it in the
`android` program which comes with the Android sdk and lists AVDs. 
$ANDROID_SDK is the path to your sdk.
$ANDROID_SKIN is one of the valid skins listed in the `android` program:

<img src="{{ ASSET_PATH }}/skins.png" />


## Android FastDev Launching

Before trying to launch you should put

{% highlight bash %}
ANDROID_SDK=/path/to/androidsdk/
{% endhighlight %}

into your `.bashrc`.

First, you'll need to launch the emulator.
Then,
you can use the `titanium.py` script to start the fastdev server: 

{% highlight bash %}
$ tsm run 2.0.1 fastdev start
{% endhighlight %}

Now that you've started the server, you can launch the app:

{% highlight bash %}
$ tsm run 2.0.1 run --platform=android
{% endhighlight %}

## Cleaning the project

To clean the project you can just touch the tiapp.xml:

{% highlight bash %}
$ touch tiapp.xml
{% endhighlight %}

## Conclusion

Unfortunately the `titanium.py` script can't launch the emulator or build iOS
distributables.  But, in combination with the `builder.py` scripts 
you can pretty much completely ignore Titanium Studio.

I recommend using some kindof Makefile (or Cakefile, Jakefile, Rakefile, etc)
to manage all of these commands.

Hope this helps.
