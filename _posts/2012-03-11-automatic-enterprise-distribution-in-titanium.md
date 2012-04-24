---
layout: post
title: "Automatic iOS Enterprise Distribution in Titanium"
category: 
tags: [tutorial, titanium]
---
{% include JB/setup %}

So let's say you've got an Enterprise Distribution License with Apple, and
you're writing an app with Appcelerator's Titanium. You want to be able to
deploy an `.ipa` to your server with a single command. You'll need to automate
a couple things:

1. Building the project in Titanium
2. Code signing to produce an `.ipa`
3. Uploading to your server
4. Updating your `.plist` file to point to the correct `.ipa` or setting up a
   symbolic link so the `.plist` will point to the current `.ipa`.

I use `make` in my project to handle the automation.  Below are snippets of
some of my `Makefile` rules which should help you automate your build process.

## Automating the Titanium Build

{% highlight make %}
ios-app : os-sanity update-rev
   @echo "Executing Titanium build script for iPhone .."
   @$(PYTHON) $(IOS_BUILDER) distribute $(IOS_VER) "`pwd`"\
      $(IOS_ID) $(APP_NAME) $(MOBILEPROVISION) \
      $(DEVIDENT) "`pwd`/build/iphone/" universal | egrep -v '[DEBUG]'
{% endhighlight %}

This rule assumes that the current directory is a Titanium project (hence the
`pwd`).  It requires a couple of make variables:

* `PYTHON`: path of python executable, like /usr/local/bin
* `IOS_BUILDER`: path of the Titanium iOS builder.py.  This is probably
  something like
  `/Library/Application Support/Titanium/mobilesdk/osx/1.8.0.1/iphone/builder.py`
* `IOS_VER`: The iOS sdk to build against.  For example, `4.0` or `5.0`.
* `IOS_ID`: The iOS App ID.  For example, `com.myapp.example`. This must match
  the ID associated with the Enterprise Distribution cert you will use.
* `APP_NAME`: The name of the application.
* `MOBILEPROVISION`: The ID of the mobile provisioning profile.  You can find
  this in the Xcode organizer: hit devices on the top, then hit provisioning
  profiles on the side.  The id will look something like
  `D45H6AD7-J6CC-95A3-HH65-8DJD0583D633`.
* `DEVIDENT`: Developer identity.  This is what appears after 'iPhone
  Distribution:' in your developer identity.  So, if your developer identity is
  'iPhone Distribution: Example co.', you would set this to 'Example co.'.

I also pipe this through `egrep` to get rid of all debug messages for cleaner
output.

This will compile a `.app` for you; now, we'll need to sign it to create an
`.ipa` for distribution.

## Automatic Code Signing

{% highlight make %}
codesign: 
   /usr/bin/xcrun -sdk iphoneos PackageApplication -v \
      "`pwd`/build/iphone/build/Release-iphoneos/$(APP_NAME).app" \
      -o "`pwd`/build/$(APP_NAME).ipa" \
      --sign $(DISTRIB_PROFILE) \
      --embed "`pwd`/build/iphone/build/Release-iphoneos/$(APP_NAME).app/embedded.mobileprovision"
{% endhighlight %}

This rule will use `xcrun` to perform the actual packaging of the app.  Only
one additional variable is introduced:

* `DISTRIB_PROFILE`: The developer identity used for distribution.  This
  includes the 'iPhone Distribution:' part, so it should look something like
  'iPhone Distribution: Example co.'.

This will create an `.ipa` file in your build folder.  This will need to be
copied to the server which will be distributing your application.

## Deploying

{% highlight make %}
deploy:
   scp build/$(APP_NAME).ipa deploy@server:ipa/$(APP_NAME)-$(GITDESCRIBE).ipa
   ssh deploy@server ln -fs \~/ipa/$(APP_NAME)-$(GITDESCRIBE).ipa \~/ipa/$(APP_NAME).ipa
{% endhighlight %}

These two lines simply scp the file over and then symlink your app name to the
latest version of your app.  `$(GITDESCRIBE)` could be any type of description
of the current version; in my case it's `git describe --always`. If you're
using svn or mercurial or (gasp) cvs, set this variable to whatever identifies
the current checked out revision.

Putting it all together, we have the following rule:

{% highlight make %}
distribute: ios-app codesign deploy
{% endhighlight %}

and your latest version is on your server, waiting to be downloaded.

## Automatic updating, too!

The only problem thus far is that your enterprise customers don't get notified
when you make a new release.  Even if they did, they'd have to manually go and
download the new version.  How tedious.  Luckilly, with a bit of simple
Javascript, we can remedy the situation.

You'll first have to find a way for the server to be able to report the current
version available.  I do this by having a `motd.json` file with a `version`
field which indicates the current client `.ipa` available on the server.  When
I deploy, I have a rule which will shell into the deployment server and `sed`
that line in the `motd.json` file.  

Every time the client is started, it pulls this `motd.json` file and checks
its version against the server's version.  If they differ, it pulls a new
`.ipa` off the server.

Not the most elegant solution, I know; but,
as I only write a single app at the moment, I haven't yet invested the time
into writing some kindof open source deployment architecture.  It's an idea,
though.

Your `motd.json` may look something like:

{% highlight javascript %}
{
   "version":"v1.0-1-gb25fdee"
}
{% endhighlight %}

So, we'll write a simple rule to sed this to be the current `$(GITDESCRIBE)`:

{% highlight make %}
update-motd:
   ssh deploy@server sed -i\'.bak\' -e \'s/\"version\":\".*\"/\"version\":\"$(GITDESCRIBE)\"/\' public_html/motd.json
{% endhighlight %}

Your client app must also have knowledge of it's current revision.  You can
do this by creating a `revision.js` file in your `Resources` directory:

{% highlight make %}
update-rev:
   @echo 'module.exports = $(GITDESCRIBE);' > Resources/revision.js
{% endhighlight %}

This file can now be `require()`d to obtain the current version.  Now, your
distribute rule will look like this:

{% highlight make %}
distribute: update-rev ios-app codesign deploy update-motd
{% endhighlight %}

Lastly, we'll need a bit of Javascript to tie it all together.  We'll need to
pull `motd.json` from the server, check the version, and `openURL` an
`itms-services` url in order to start the upgrade.  The following example is
very bare; you most likely want to pop up a window informing the user that
an update is available:

{% highlight javascript %}
// update.js
function check () {
   var url = "http://example.com/motd.json",
       client = Ti.Network.createHTTPClient();

   client.onload = function (e) {
      try {
         var data = JSON.parse(this.responseText),
             version = require('version');

         if (data.version != version) {
            // Actually trigger the update
            // This will display the 'example.com wants to install..' box
            Ti.Platform.openURL("itms-services://?action=download-manifest&url=https://example.com/App.plist");
         }
      } catch (e) {
         alert('error checking for update');
      }
   };

   client.onerror = function (e) {
      alert('error checking for update');
   }
}

module.exports = check;
{% endhighlight %}

To use this snippet, we'll have to require it elsewhere and invoke the `check()`
function:

{% highlight javascript %}
var update = require('update');
update.check();
{% endhighlight %}

When the above bit is run, a box will pop up saying 'Developer would like to
install Application' when an update is available.

This is a great way to quickly deploy to enterprise customers.  You may also
be able to use a similar method to the above for deploying to beta testers,
though I have not tested this.

Enjoy & good luck.
