---
title: 'HAProxy: Gathering Stats Using `socat`'
author: Michael Goffin
layout: post
permalink: /2010/07/14/haproxy-gathering-stats-using-socat/
tt_auto_tweet:
  - 'false'
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - http://is.gd/FynnNa
categories:
  - General
tags:
  - haproxy
  - socat
---
We use <a href="http://www.zabbix.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.zabbix.com/', 'Zabbix']);" >Zabbix</a> to monitor our systems at work. It&#8217;s a great open source alternative. One of things I&#8217;ve been working on recently is auditing our monitoring system for defunct monitoring points, unmonitored services, and proper triggers and alerts based on our SLA requirements. <a href="http://haproxy.1wt.eu/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://haproxy.1wt.eu/', 'HAProxy']);" >HAProxy</a> was one of those items. 

There are standard monitoring points like PID changes, web interface availability, CPU/Memory usage, etc. But what about monitoring things like MAXCONN and CURCONNS? Turns out there&#8217;s a way to get this data from HAProxy using what they call a &#8220;stats socket.&#8221; This information isn&#8217;t found in the **haproxy-en.txt** file, but in the **configuration.txt** file. In my installation, it isn&#8217;t in **/usr/share/doc/haproxy** like everything else. I actually found this on the official <a href="http://haproxy.1wt.eu/download/1.4/doc/configuration.txt" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://haproxy.1wt.eu/download/1.4/doc/configuration.txt', 'website']);" >website</a>. Here&#8217;s the interesting bit:

> stats socket <path> [{uid | user} <uid>] [{gid | group} <gid>] [mode <mode>]  
> [level <level>]
> 
> Creates a UNIX socket in stream mode at location <path>. Any previously  
> existing socket will be backed up then replaced. Connections to this socket  
> will return various statistics outputs and even allow some commands to be  
> issued. Please consult section 9.2 &#8220;Unix Socket commands&#8221; for more details.
> 
> An optional &#8220;level&#8221; parameter can be specified to restrict the nature of  
> the commands that can be issued on the socket :  
> &#8211; &#8220;user&#8221; is the least privileged level ; only non-sensitive stats can be  
> read, and no change is allowed. It would make sense on systems where it  
> is not easy to restrict access to the socket.
> 
> &#8211; &#8220;operator&#8221; is the default level and fits most common uses. All data can  
> be read, and only non-sensible changes are permitted (eg: clear max  
> counters).
> 
> &#8211; &#8220;admin&#8221; should be used with care, as everything is permitted (eg: clear  
> all counters).
> 
> On platforms which support it, it is possible to restrict access to this  
> socket by specifying numerical IDs after &#8220;uid&#8221; and &#8220;gid&#8221;, or valid user and  
> group names after the &#8220;user&#8221; and &#8220;group&#8221; keywords. It is also possible to  
> restrict permissions on the socket by passing an octal value after the &#8220;mode&#8221;  
> keyword (same syntax as chmod). Depending on the platform, the permissions on  
> the socket will be inherited from the directory which hosts it, or from the  
> user the process is started with. 

Simple enough. Edit your **haproxy.cfg** and add this into your &#8220;global&#8221; section:

<pre>global
        daemon
        maxconn 100
        quiet
        user haproxy
        group haproxy
        stats socket    /tmp/haproxy
</pre>

<a href="http://www.mgoff.in/2010/04/18/haproxy-reloading-your-config-with-minimal-service-impact/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mgoff.in/2010/04/18/haproxy-reloading-your-config-with-minimal-service-impact/', 'Reload your HAProxy config']);" >Reload your HAProxy config</a> and you should now see a socket setup in **/tmp** (note in the <a href="http://www.freebsd.org/cgi/man.cgi?query=ls" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.freebsd.org/cgi/man.cgi?query=ls', 'ls']);" >ls</a> output that the &#8220;s&#8221; at the beginning of the permission set denotes the file type as a socket):

<pre># ls -lah /tmp/haproxy
srwxr-xr-x 1 root root 0 2010-07-14 12:53 /tmp/haproxy
#
</pre>

Now we can query HAProxy using this socket for some stats. A great way to do this is using <a href="http://www.dest-unreach.org/socat/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.dest-unreach.org/socat/', 'socat']);" >socat</a>. If you don&#8217;t have it installed, you can compile from <a href="http://www.dest-unreach.org/socat/download/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.dest-unreach.org/socat/download/', 'source']);" >source</a>, or use the package management system for your OS (ex: &#8220;apt-get install socat&#8221; for Ubuntu). 

To query for some stats, you can try the following commands:

<pre># echo “show info” | socat unix-connect:/tmp/haproxy stdio
# echo “show stat” | socat unix-connect:/tmp/haproxy stdio
# echo “show errors” | socat unix-connect:/tmp/haproxy stdio
# echo “show sess” | socat unix-connect:/tmp/haproxy stdio
</pre>

More information on interacting with HAProxy through the stats socket can be found in section **&#8220;9.2. Unix Socket commands&#8221;** of the configuration.txt file I linked to above (it&#8217;s the last section in the file).

<div class="tweetthis" style="text-align:right;">
  <p>
    <a href="http://twitter.com/intent/tweet?text=HAProxy%3A+Gathering+Stats+Using+%60socat%60+http%3A%2F%2Fis.gd%2FFynnNa" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://twitter.com/intent/tweet?text=HAProxy%3A+Gathering+Stats+Using+%60socat%60+http%3A%2F%2Fis.gd%2FFynnNa', '']);" class="tt"  title="Post to Twitter"><img class="nothumb" src="http://www.mgoff.in/wp-content/plugins/tweet-this/icons/en/twitter/tt-twitter-micro4.png" alt="Post to Twitter" /></a>
  </p>
</div>