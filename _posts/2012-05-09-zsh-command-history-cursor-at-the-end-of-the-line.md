---
title: 'ZSH Command History: Cursor at the end of the line!'
author: Michael Goffin
layout: post
permalink: /2012/05/09/zsh-command-history-cursor-at-the-end-of-the-line/
tweet_this_url:
  - http://is.gd/Nd7maS
categories:
  - Debian
  - ZSH
tags:
  - Debian
  - ZSH
---
About five or so years ago, Debian decided to mess with their <a href="http://www.zsh.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.zsh.org/', 'ZSH']);" >ZSH</a> package. One of the things that changed is the cursor location when browsing your command history. On every other OS, when you browse your command history, whether in vi mode (viins or vicmd) or in emacs mode, the cursor always goes to the end of the command. Debian decided to change it so that it goes to the beginning of the line. WTF! 

This isn&#8217;t a huge change, but when you are going back and forth between systems all day and the same action has different results, it can get pretty infuriating. There was really no need to change this whatsoever. Unfortunately, the <a href="http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=383737" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=383737', 'stance']);" >stance</a> they have is:

{% raw %}
> tag 383737 + wontfix  
> quit
> 
> Unfortunately this boils down to a binary division in a fundamental belief,  
> and either way people are unhappy. 
{% endraw %}

Essentially, they decided to make the change to be non-compliant with the rest of the ZSH community, and then call it a binary division as an excuse for their change. Nice. 

The issue manifested because they decided to put values in the global **/etc/zsh/zshrc** which causes this behavior. There&#8217;s horrible hacks you can do in your personal **.zshrc** to sort of fix the functionality, or if you have sudo on the box you can change the behavior for everyone by altering the global rc file. However, there is a much easier way shown to me by **<a href="http://www.zi0r.com" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.zi0r.com', 'zi']);" >zi</a>**. I hadn&#8217;t seen this solution anywhere I searched so I thought I&#8217;d write it up for anyone who wants a clean fix:

{% highlight bash %}
% echo 'unsetopt global_rcs' >> ~/.zprofile
{% endhighlight %}

Fixed! You&#8217;ll have to invoke a new shell for this to take effect of course. What this does is it tells ZSH to ignore the global rc file before loading your own. The only drawback from this is that if there are any specific things from the global rc file that you wanted, you&#8217;ll have to copy them into your own. Is it a pain that we have to do this at all just because Debian wants to be different? Sure. But it&#8217;s a few extra seconds tacked onto copying your own .zshrc file to the machine that I am more than happy to take to get ZSH behaving like it should!
