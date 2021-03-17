---
title: Browser Memory Issues With Sholes.info 1.1 ROM
author: Michael Goffin
layout: post
permalink: /2010/01/18/browser-memory-issues-with-sholes-info-1-1-rom/
tt_auto_tweet:
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - https://is.gd/1qRqU1
categories:
  - Android
tags:
  - Android
---
With the release of the <a href="https://www.sholes.info/2010/01/16/2-0-1-sholes-info-edition-1-1-release/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.sholes.info/2010/01/16/2-0-1-sholes-info-edition-1-1-release/', 'Sholes.info 1.1 ROM']);" >Sholes.info 1.1 ROM</a> for the Droid, there&#8217;s been some concern over performance issues as the phone&#8217;s uptime increases. It seems one problem is that the browser will continue to chew on memory, and with compcache setup in this ROM, it chews through the swap as well. If you are enjoying the features and functionality of the 1.1 ROM, but would like to disable compcache to help limit the browser&#8217;s memory consumption and try to keep your phone alive a bit longer, there is a way to disable it:

{% highlight bash %}
[root@droid] / # sysrw && mkdir -p /system/etc/rc.d/ && touch /system/etc/rc.d/disable_15_compcache.sh; sysro; sync
{% endhighlight %}

Another tip I'll throw in as a free bonus is to get SetCPU from the Market. Some people (myself included) have seen problems where the phone takes a while to wake from sleep, or respond to an unlock swipe (getting a phone call makes it near impossible to get it to wake up). Enable profiles in SetCPU and only enable the Sleep profile. Edit it and set the max/min CPU to 250. People have said this seems to work helping get the phone to wake up quickly. 

As a head's up, it seems that people have started picking up the OTA updates. Seems a little early, and no one I know directly has a confirmed update. Then again, we're running 1.1 and it disables OTA so we can work on it before updating.
