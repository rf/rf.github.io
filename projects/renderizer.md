---
layout: page
title: Renderizer
---
{% include JB/setup %}

*python*

Renderizer is a Titanium build plugin that automates image rendering. It can
drive Adobe Illustrator or Inkscape. Given a `images.yaml` configuration file,
it can render images depending on the platform you're currently building for
with Titanium.

It also supports plugins of its own, one of which allows for the easy creation
of 9patch graphics by using an extra layer in Inkscape.

Check out the [GitHub page for more information](https://github.com/rf/renderizer).

The `images.yaml` file would look something like this:

{% highlight yaml %}

icons:
   backend: illustrator

   output:
      - path: Resources/images/ipod
        dpi: 72

      - path: Resources/images/ipod
        dpi: 144
        append: '@2x'

      - path: Resources/images/ipad
        dpi: 82

   images:
      - icons/foo.ai
      - icons/bar.ai
      - icons/baz.ai
      - icons/foobar.ai

androidui:
   backend: illustrator
   platform: android

   output:
      - path: Resources/images/android
        dpi: 90

   images:
      - navbar.ai
      - background.ai

{% endhighlight %}
