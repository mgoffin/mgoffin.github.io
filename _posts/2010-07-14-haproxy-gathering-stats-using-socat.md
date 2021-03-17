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
  - https://is.gd/FynnNa
categories:
  - General
tags:
  - haproxy
  - socat
---
We use <a href="https://www.zabbix.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.zabbix.com/', 'Zabbix']);" >Zabbix</a> to monitor our systems at work. It's a great open source alternative. One of things I've been working on recently is auditing our monitoring system for defunct monitoring points, unmonitored services, and proper triggers and alerts based on our SLA requirements. <a href="https://haproxy.1wt.eu/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://haproxy.1wt.eu/', 'HAProxy']);" >HAProxy</a> was one of those items. 

There are standard monitoring points like PID changes, web interface availability, CPU/Memory usage, etc. But what about monitoring things like MAXCONN and CURCONNS? Turns out there's a way to get this data from HAProxy using what they call a "stats socket." This information isn't found in the **haproxy-en.txt** file, but in the **configuration.txt** file. In my installation, it isn't in **/usr/share/doc/haproxy** like everything else. I actually found this on the official <a href="https://haproxy.1wt.eu/download/1.4/doc/configuration.txt" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://haproxy.1wt.eu/download/1.4/doc/configuration.txt', 'website']);" >website</a>. Here's the interesting bit:

{% highlight bash %}
> stats socket <path> [{uid | user} <uid>] [{gid | group} <gid>] [mode <mode>]  
> [level <level>]
> 
> Creates a UNIX socket in stream mode at location <path>. Any previously  
> existing socket will be backed up then replaced. Connections to this socket  
> will return various statistics outputs and even allow some commands to be  
> issued. Please consult section 9.2 "Unix Socket commands" for more details.
> 
> An optional "level" parameter can be specified to restrict the nature of  
> the commands that can be issued on the socket :  
> - "user" is the least privileged level ; only non-sensitive stats can be  
> read, and no change is allowed. It would make sense on systems where it  
> is not easy to restrict access to the socket.
> 
> - "operator" is the default level and fits most common uses. All data can  
> be read, and only non-sensible changes are permitted (eg: clear max  
> counters).
> 
> - "admin" should be used with care, as everything is permitted (eg: clear  
> all counters).
> 
> On platforms which support it, it is possible to restrict access to this  
> socket by specifying numerical IDs after "uid" and "gid", or valid user and  
> group names after the "user" and "group" keywords. It is also possible to  
> restrict permissions on the socket by passing an octal value after the "mode"  
> keyword (same syntax as chmod). Depending on the platform, the permissions on  
> the socket will be inherited from the directory which hosts it, or from the  
> user the process is started with. 
{% endhighlight %}

Simple enough. Edit your **haproxy.cfg** and add this into your "global" section:

{% highlight bash %}
global
        daemon
        maxconn 100
        quiet
        user haproxy
        group haproxy
        stats socket    /tmp/haproxy
{% endhighlight %}

and you should now see a socket setup in **/tmp** (note in the <a href="https://www.freebsd.org/cgi/man.cgi?query=ls" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.freebsd.org/cgi/man.cgi?query=ls', 'ls']);" >ls</a> output that the "s" at the beginning of the permission set denotes the file type as a socket):

{% highlight bash %}
# ls -lah /tmp/haproxy
srwxr-xr-x 1 root root 0 2010-07-14 12:53 /tmp/haproxy
#
{% endhighlight %}

Now we can query HAProxy using this socket for some stats. A great way to do this is using <a href="https://www.dest-unreach.org/socat/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.dest-unreach.org/socat/', 'socat']);" >socat</a>. If you don't have it installed, you can compile from <a href="https://www.dest-unreach.org/socat/download/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.dest-unreach.org/socat/download/', 'source']);" >source</a>, or use the package management system for your OS (ex: "apt-get install socat" for Ubuntu). 

To query for some stats, you can try the following commands:

{% highlight bash %}
# echo “show info” | socat unix-connect:/tmp/haproxy stdio
# echo “show stat” | socat unix-connect:/tmp/haproxy stdio
# echo “show errors” | socat unix-connect:/tmp/haproxy stdio
# echo “show sess” | socat unix-connect:/tmp/haproxy stdio
{% endhighlight %}

More information on interacting with HAProxy through the stats socket can be found in section **"9.2. Unix Socket commands"** of the configuration.txt file I linked to above (it's the last section in the file).
