---
layout: page
title: "Moar"
---
{% include JB/setup %}

*node.js javascript*

While writing [nd](nd.html) I wanted to support both linux and windows, but
I needed a way to paginate the documentation render in the windows console,
so I wrote a small page abstraction. On Linux it'll just invoke your $PAGER,
on Windows it'll fall back to a pure javascript pager implementation.

More information available on [the Github page](https://github.com/rf/moar).
