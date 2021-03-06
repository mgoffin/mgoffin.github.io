---
title: 'HAProxy: Reloading Your Config With Minimal Service Impact'
author: Michael Goffin
layout: post
permalink: /2010/04/18/haproxy-reloading-your-config-with-minimal-service-impact/
tt_auto_tweet:
  - 'false'
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - https://is.gd/FCO5TO
categories:
  - General
tags:
  - haproxy
---
<a href="https://haproxy.1wt.eu/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://haproxy.1wt.eu/', 'HAProxy']);" >HAProxy</a> is a high performance load balancer. It is very light-weight, and free, making it a great option if you are in the market for a load balancer and need to keep your costs down. 

Lately we've been making a lot of load balancer changes at work to accommodate new systems and services. Even though we have two load balancers running with <a href="https://www.keepalived.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.keepalived.org/', 'keepalived']);" >keepalived</a> taking care of any failover situations, I was thinking about how we go about reloading our configuration files. In the event of a change, the &#8220;common&#8221; way to get the changes to take effect is to run **/etc/init.d/haproxy restart**. This is bad for a couple major reasons:

  1. You are temporarily shutting your load balancer down
  2. You are severing any current connections going through the load balancer

You might say, &#8220;if you have two load balancers with keepalived, restarting the service should be fine since keepalived will handle the failover.&#8221; This, however, isn't always true. Keepalived uses advertisements to determine when to fail over. The default advertisement interval is 1 second (configurable in keepalived.conf). The skew time helps to keep everyone from trying to transition at once. It is a number between 0 and 1, based on the formula **(256 &#8211; priority) / 256**. As defined in the RFC, the backup must receive an advertisement from the master every **(3 * advert\_int) + skew\_time** seconds. If it doesn't hear anything from the master, it takes over. 

Let's assume you are using the default interval of 1 second. On my test machine, this is the duration of time it takes to restart haproxy:

{% highlight bash %}
# time /etc/init.d/haproxy restart
 * Restarting haproxy haproxy
   ...done.

real    0m0.022s
user    0m0.000s
sys     0m0.016s
{% endhighlight %}

In this situation, haproxy would restart much faster than your 1 second interval. You could get lucky and happen to restart it just before the check, but luck is not consistent enough to be useful. Also, in very high-traffic situations, you'll be causing a lot of connection issues. So we cannot rely on keepalived to solve the first problem, and it definitely doesn't solve the second problem. 

After sifting through haproxy documentation (the text-based documentation, not the man page) (/usr/share/doc/haproxy/haproxy-en.txt.gz on Ubuntu), I came across this:

{% highlight bash %}
313
314     global
315         daemon
316         quiet
317         nbproc  2
318         pidfile /var/run/haproxy-private.pid
319
320     # to stop only those processes among others :
321     # kill $(</var/run/haproxy-private.pid)
322
323     # to reload a new configuration with minimal service impact and without
324     # breaking existing sessions :
325     # haproxy -f haproxy.cfg -p $(</var/run/haproxy-private.pid) -st $(</var/run/haproxy-private.pid)
{% endhighlight %}

That last command is the one of interest. The -p asks the process to write down each of its children's pids to the specified pid file, and the -st specifies a list of pids to send a SIGTERM to after startup. But it does this in an interesting way:

{% highlight bash %}
609 The '-st' and '-sf' command line options are used to inform previously running
610 processes that a configuration is being reloaded. They will receive the SIGTTOU
611 signal to ask them to temporarily stop listening to the ports so that the new
612 process can grab them. If anything wrong happens, the new process will send
613 them a SIGTTIN to tell them to re-listen to the ports and continue their normal
614 work. Otherwise, it will either ask them to finish (-sf) their work then softly
615 exit, or immediately terminate (-st), breaking existing sessions. A typical use
616 of this allows a configuration reload without service interruption :
617
618  # haproxy -p /var/run/haproxy.pid -sf $(cat /var/run/haproxy.pid)
{% endhighlight %}

The end-result is a reload of the configuration file which is not visible by the customer. It also solves the second problem! Let's look at an example of the command and look at the time compared to our above example:

{% highlight bash %}
# time haproxy -f /etc/haproxy.cfg -p /var/run/haproxy.pid -sf $(cat /var/run/haproxy.pid)

real    0m0.018s
user    0m0.000s
sys     0m0.004s
{% endhighlight %}

I've specified the config file I want to use and the pid file haproxy is currently using. The **$(cat /var/run/haproxy.pid)** takes the output of **cat /var/run/haproxy.pid** and passes it in to the -sf parameter as a list, which is what it is expecting. You will notice that the time is actually faster too (.012s sys, and .004s real). It may not seem like much, but if you are dealing with very high volumes of traffic, this can be pretty important. Luckily for us it doesn't matter because we've been able to reload the haproxy configuration without dropping any connections and without causing any customer-facing issues. 

**UPDATE:** There is a reload in some of the init.d scripts (I haven't checked every OS, so this can vary), but it uses the -st option which will break existing sessions, as opposed to using -sf to do a graceful hand-off. You can modify the haproxy_reload() function to use the -sf if you want. I also find it a bit confusing that the documentation uses **$(cat /path/to/pidfile)** whereas this haproxy_reload() function uses **$(<$PIDFILE)**. Either should work, but really, way to lead by example&#8230;
