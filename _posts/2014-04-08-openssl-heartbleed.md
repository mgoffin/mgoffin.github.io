---
title: OpenSSL Heartbleed
author: Michael Goffin
layout: post
permalink: /2014/04/08/openssl-heartbleed/
tweet_this_url:
  - http://is.gd/9Amle9
categories:
  - OpenSSL
  - Python
tags:
  - OpenSSL
  - Python
---
People are hard at work today patching <a href="https://www.openssl.org/‎" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.openssl.org/‎', 'OpenSSL']);" >OpenSSL</a> due to the <a href="http://heartbleed.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://heartbleed.com/', 'Heartbleed']);" >Heartbleed</a> bug (CVE-2014-0160). 

There&#8217;s tons of information pouring out as package repositories are rapidly updating to the latest OpenSSL 1.0.1g release that came out yesterday. If you want to see if your server is vulnerable, you can run this:

{% highlight bash %}
openssl version -a
{% endhighlight %}

If you are anywhere in the 1.0.1 to 1.0.1f (inclusive) range or have a compile time earlier than yesterday, you should look to upgrade. For Ubuntu servers, you can find information on how to upgrade <a href="https://gist.github.com/coderanger/10084033" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://gist.github.com/coderanger/10084033', 'here']);" >here</a>, or <a href="https://gist.github.com/psykode/10111495" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://gist.github.com/psykode/10111495', 'here']);" >here</a> if you are running Lucid (10.04). There&#8217;s also a useful python <a href="https://gist.github.com/sh1n0b1/10100394" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://gist.github.com/sh1n0b1/10100394', 'script']);" >script</a> that will allow you to test your sites for being vulnerable. **Do not** use that script for anything other than testing your own sites! I&#8217;m sure we all have enough to deal with today :)
