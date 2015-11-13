---
title: Android System Debug Output With Logcat
author: Michael Goffin
layout: post
permalink: /2010/02/09/android-system-debug-output-with-logcat/
tt_auto_tweet:
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - http://is.gd/GjzQdh
categories:
  - Android
tags:
  - Android
  - logcat
---
I&#8217;ve been asked many times how one might get some logging information when doing work on an Android phone. The answer has always been, and always will be &#8220;use <a href="http://developer.android.com/guide/developing/tools/adb.html#logcat" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://developer.android.com/guide/developing/tools/adb.html#logcat', 'logcat']);" >logcat</a>.&#8221; Just to be clear, logcat isn&#8217;t just some easy way do something like \`cat /var/log/syslog\`. It&#8217;s actually quite powerful. I always recommend having a terminal open and running logcat whenever you&#8217;re doing something on your phone or your emulator. It helps tracking down little nasties, and is a huge help when reporting bugs. 

I&#8217;d write more on this, but Google has done an excellent job with the documentation. The only thing I would stress is getting very familiar with filtering log output. It will save you a lot of time if you know exactly what you&#8217;re looking for. Another tip that&#8217;s hidden at the bottom of the documentation is that by default, **stdout** and **stderr** are redirected to **/dev/null**. You can redirect them for visibility in logcat by doing the following:

<pre>$ adb shell stop
$ adb shell setprop log.redirect-stdio true
$ adb shell start
</pre>

You can also set this by default in **/data/local.prop**.

<div class="tweetthis" style="text-align:right;">
  <p>
    <a href="http://twitter.com/intent/tweet?text=Android+System+Debug+Output+With+Logcat+http%3A%2F%2Fis.gd%2FGjzQdh" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://twitter.com/intent/tweet?text=Android+System+Debug+Output+With+Logcat+http%3A%2F%2Fis.gd%2FGjzQdh', '']);" class="tt"  title="Post to Twitter"><img class="nothumb" src="http://www.mgoff.in/wp-content/plugins/tweet-this/icons/en/twitter/tt-twitter-micro4.png" alt="Post to Twitter" /></a>
  </p>
</div>