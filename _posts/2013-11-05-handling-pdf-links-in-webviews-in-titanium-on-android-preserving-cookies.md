---
layout: post
title: "Handling PDF links in webviews in Titanium on Android, preserving cookies"
category: 
tags: []
---
{% include JB/setup %}

This will be one of my last posts regarding Titanium because I'm rewriting my app
using native SDKs; this will be the subject of a future post.

I needed to fix the handling of PDFs inside of webviews in Titanium on Android.
This seems to be a well-known problem on Android; apparently, not only does
the browser not handle PDFs, but we can't simply launch an intent with a PDF
URL. It's bizarre, but this just doesn't work. The PDF must be downloaded first
before we can launch the intent. I'm not sure why this is, but I did try
launching the intent with the URL first and this didn't work; also, googling
this issue reveals several StackOverflow questions where these results are
corroborated.

So, we have to download the file first. There's plenty of example code available
for writing an Android activity to do this in Java, but, of course, this is
hard to fit into Titanium. So, the approach I took was to add a new event that
is fired by WebViews when links that end in PDF are hit. This event also
includes any cookies that were set by the URL. This is so we can preserve
any authentication that occurred in the webview when we download the PDF.

This approach does require a modification to be made to the Titanium SDK. It
also requires a bit of code to download the PDF, move it to a temporary
directory (since a PDF reader app can't access your app's data directory), and
launch the intent.

The code I wrote is available in this gist: https://gist.github.com/rf/7326196
To use it:

  1. Apply the diff at the bottom to a copy of the Titanium SDK source.
  2. Compile the Titanium SDK and install it.
  3. Copy the `pdf.js` code into your project.
  4. Add the event listener to your webviews for the event `pdf` and use 
     the `pdf.js` code to handle it, as seen in the example.

I'd make a PR and try to get this merged
into the Titanium codebase, but since it requires a lot of work on the app side
to be useful, I'm not sure they'd be interested. Plus, I'm moving off of
Titanium anyway. I hope this is useful to some of you!
