---
title: Late Night Productivity
author: Michael Goffin
layout: post
permalink: /2010/01/12/late-night-productivity/
tweet_this_url:
  - http://is.gd/FCf8CD
categories:
  - General
tags:
  - linode
  - nginx
  - php
---
For the fifth day in a row I find myself awake at almost 1am, and there&#8217;s no sign of being tired in sight. But being a geek has its advantages when this happens. While most people would complain about being awake, try to read a book to coax their body to sleep, or just toss-and-turn in bed, I get to enjoy hacking on stuff. It&#8217;s amazing how much more productive I get as the night goes on. This is the sort of schedule I was on in college. I&#8217;d spend my days cranking through classes and work, rip through homework as quickly as possible, and then spend the rest of the night learning stuff. 

With my late night tonight, I setup this domain, purchased a VPS from <a href="https://www.linode.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.linode.com/', 'Linode']);" >Linode</a>, and built the site. I think the biggest pain was configuring <a href="http://nginx.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://nginx.org/', 'nginx']);" >nginx</a> with php-fastcgi support. On that note, I find it horribly frustrating that nginx, one of the most lightweight and speedy web servers, causes so much <a href="http://library.linode.com/web-servers/nginx/php-fastcgi-ubuntu9.10-karmic" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://library.linode.com/web-servers/nginx/php-fastcgi-ubuntu9.10-karmic', 'pain']);" >pain</a> to get PHP working. I hardly think that installing spawn-fcgi, and writing a bunch of custom executables and init scripts is an adequate way to go about doing it. But hey, at least it works. 

I guess I&#8217;ll get back to working on the site. If I can&#8217;t get tired soon, I might as well take a shower and go back to work&#8230;
