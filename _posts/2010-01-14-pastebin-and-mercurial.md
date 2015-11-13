---
title: Pastebin and Mercurial
author: Michael Goffin
layout: post
permalink: /2010/01/14/pastebin-and-mercurial/
tt_auto_tweet:
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - http://is.gd/lgVRwR
categories:
  - General
tags:
  - apache
  - fcgi
  - hg
  - nginx
  - pastebin
---
I decided to start adding additional functionality to this site. As much as I feel like I&#8217;ll benefit from having a place to focus my mental energy, having the tools to assist in that focus would be a huge plus. The first two things I thought of were a personal <a href="http://www.pastebin.com" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.pastebin.com', 'pastebin']);" >pastebin</a>, and a revision control system. 

<a href="http://p.mgoff.in" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://p.mgoff.in', 'Pastebin']);" >Pastebin</a> was fairly easy to get going. Since I&#8217;m using <a href="http://nginx.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://nginx.org/', 'Nginx']);" >Nginx</a>, the only pain was setting up the scripts for FCGI (as noted in my previous post). After that it was quite simple. The one problem I had was the SQL provided for pastebin didn&#8217;t work. It bombed on one of the KEYS. I didn&#8217;t feel like worrying about it, so I configured it to use files instead. Just as easy. 

For a revision control system, I decided to try <a href="http://mercurial.selenic.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://mercurial.selenic.com/', 'Mercurial']);" >Mercurial</a>. I&#8217;ve heard good things about it, and I know a few people who are running it as well. I also wanted to get a <a href="http://m.mgoff.in/hg/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://m.mgoff.in/hg/', 'web frontend']);" >web frontend</a> working. Nothing fancy, just enough to see what&#8217;s going on. My main goal for this was to put my rc files in an rcs of some sort so I can continue to modify them and review changes. I&#8217;ve been modifying them a lot lately, and found that bouncing back and forth between changes resulted in **a lot** of commented lines. It&#8217;ll also be nice to pull my rc files onto whatever machine I&#8217;m on, or if I can&#8217;t install hg, I can always just get them off the website. This was a little tougher to install. Most people suggest using Nginx as a proxy and use Apache as the backend server. I wound up going that route after many attempts as getting FCGI in Nginx playing nicely with Mercurial. But hey, now I have a place to house my rc files. Well worth the effort.

<div class="tweetthis" style="text-align:right;">
  <p>
    <a href="http://twitter.com/intent/tweet?text=Pastebin+and+Mercurial+http%3A%2F%2Fis.gd%2FlgVRwR" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://twitter.com/intent/tweet?text=Pastebin+and+Mercurial+http%3A%2F%2Fis.gd%2FlgVRwR', '']);" class="tt"  title="Post to Twitter"><img class="nothumb" src="http://www.mgoff.in/wp-content/plugins/tweet-this/icons/en/twitter/tt-twitter-micro4.png" alt="Post to Twitter" /></a>
  </p>
</div>