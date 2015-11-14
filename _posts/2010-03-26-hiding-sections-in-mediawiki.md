---
title: Hiding Sections In MediaWiki
author: Michael Goffin
layout: post
permalink: /2010/03/26/hiding-sections-in-mediawiki/
tt_auto_tweet:
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - http://is.gd/ISqCBt
categories:
  - jQuery
tags:
  - jQuery
  - MediaWiki
---
I recently implemented the <a href="http://www.mediawiki.org/wiki/Extension:Bugzilla_Reports" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mediawiki.org/wiki/Extension:Bugzilla_Reports', 'Bugzilla Reports']);" >Bugzilla Reports</a> extension at work for MediaWiki. We use MediaWiki to outline release notes and other documentation, and we include in those notes a list of related bugs that were resolved and closed. With so much manual work to write out the bugs and link to them, it made more sense to include this extension so displaying those bugs is just a simple query. But what I also noticed is that our list of bugs can be quite long depending on the size and nature of the release notes. It would be great if we could hide sections so in the event the bugs section was too huge, you can easily hide/show it. Enter <a href="http://jquery.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://jquery.com/', 'jQuery']);" >jQuery</a>! 

I won&#8217;t go into the specifics of setting up jQuery on MediaWiki, but you can find a decent summary of the steps <a href="http://www.thusa.co.za/blog/gareth-davies/getting-jquery-running-mediawiki" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.thusa.co.za/blog/gareth-davies/getting-jquery-running-mediawiki', 'here']);" >here</a>. Once you have that working, you&#8217;ll need to do the following:

  * For the skin you are using, edit the ** /<path to mediawiki install>/skins/<skin name>.php** file 
      * After the **</body>** tag and after your script tags for including jQuery, add the following: </ul> 
      {% highlight javascript %}
        <script>
    $(":header>span.mw-headline").click( function() {
        var $start = $(":header").index($(this).parent());
        var $end   = $start + 1;
        $(":header").eq($start).toggleClass("highlight");
        $(":header").eq($start).nextAll().not(":header").toggle();
        $(":header").eq($end).nextAll().not(":header").toggle();
    });
</script>
      {% endhighlight %}
        
        And that&#8217;s it! The reason we look for the span with a class of &#8220;mw-headline&#8221; is because of the &#8220;Edit&#8221; section buttons. If we look for just the header, you&#8217;ll wind up catching the Edit button and if you try to click the Edit link you&#8217;ll actually toggle the section instead. This is also why we we get the index of *$(this).parent()*. Since we clicked on an element inside the header, we don&#8217;t want to use the index of that element otherwise it won&#8217;t toggle everything correctly. 
        
        Props to <a href="http://www.mail-archive.com/jquery-en@googlegroups.com/msg40033.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mail-archive.com/jquery-en@googlegroups.com/msg40033.html', 'Andi']);" >Andi</a> for some example code. Made it easy to tweak it a little to get the Edit button working again.
