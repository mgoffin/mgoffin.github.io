---
title: CRITs Authentication
author: Michael Goffin
layout: post
permalink: /2014/06/24/crits-authentication/
tweet_this_url:
  - http://is.gd/ZIDGKd
categories:
  - General
---
Recently I&#8217;ve been asked several times about how CRITs handles authentication. The questions were less about the choices between basic, ldap, and remote authentication and more about invalid login attempts, logging, and this weird countdown timer they see when failing to authenticate properly. 

There&#8217;s a few things to understand before the bigger picture of CRITs authentication makes sense. We have three authentication sources:

  * Web Interface
  * Command-line
  * API

The web interface is fairly self-explanatory. From the command-line we developed a Django management command called <a href="https://github.com/crits/crits/wiki/runscript" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/crits/crits/wiki/runscript', 'runscript']);" >runscript</a>. This requires you to authenticate in order to run any scripts that leverage CRITs code. The API uses <a href="https://github.com/crits/crits/wiki/Authenticated-API" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/crits/crits/wiki/Authenticated-API', 'API Keys']);" >API Keys</a> that allow users to generate a unique key for each of their API needs making revoking keys less painful. 

For web and command-line authentication, the CRITs admin has the choice of forcing TOTP. This will require that the user also provide their pin and token along with their username and password. Users can opt into using TOTP even if the CRITs admin hasn&#8217;t forced people into using it. The CRITs admin can set the number of invalid login attempts before a user&#8217;s account is disabled (locked out). We log every authentication attempt to a user&#8217;s profile so they can see the last 50 attempts (successful or not) and what was involved (web auth, runscript, some basic environment info, etc.). 

When discussing the pros and cons of authentication two things came up:

  1. Brute-force lockout of accounts.
  2. <a href="http://tools.ietf.org/html/rfc6238" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://tools.ietf.org/html/rfc6238', 'Window of attack']);" >Window of attack</a> for TOTP.

When thinking about these issues, we decided we wanted to focus on increasing the duration of time it would take an attacker to brute-force lockout an account and limit the window of attack for TOTP. 

To solve this we came up with the idea of a 10 second &#8220;blackout&#8221; period after a failed login attempt. During this time period any authentication attempt, using valid credentials or not, is dropped on the floor. We log the attempt but we don&#8217;t attempt to authenticate. Once the 10 second window is up, the next authentication attempt will be paid attention to. After a successful login you can immediately login again without a window to wait for. This was important to include so people running batch jobs using runscript don&#8217;t have to wait 10 seconds before their process can continue. 

The 10 second &#8220;blackout&#8221; period is a minor inconvenience to the end-user. From a brute-force lockout perspective, it increases the amount of time an attacker needs to spend per-user. The length of time is (10s * Invalid Login Attempts) where the CRITs admin can set how many invalid login attempts are acceptable before an account is disabled. Balancing this is up to the admin as increasing the time a brute-force lockout takes increases the number of authentication attempts an attacker can try before the account is no longer viable. 

From a TOTP perspective, the window of attack becomes severely limited. In the case where an admin sets Invalid Login Attempts to 3, a user&#8217;s account will be locked out after 20 seconds of invalid authentication attempts no matter how many attempts they try during that window. Since we drop all but 3 of those on the floor, even if an attacker tries to brute force the pin + token, only 3 of them are actually used (0 seconds, 10 seconds, and 20 seconds). Now, instead of having 20 seconds of iterating over potential pin + token combinations, they only get 3 chances to get it right. We felt this limited an attacker&#8217;s ability to leverage the window of attack sufficiently. 

To reflect this in the interface, we added the countdown timer and disable the login button so it is clear to the end-user what is going on. It doesn&#8217;t prevent people from crafting requests and submitting them manually so the backend code doesn&#8217;t assume the timer prevents people from submitting attempts more than once every 10 seconds. 

There are many ways this can be enhanced. For example, instead of having the 10 second blackout period be static, you could change it so every attempt within that period resets it. This way for anyone failing more than once in the blackout period, they will always be blacked out and none of their authentication attempts will be used. Another option would be extending the blackout window to be more than 10 seconds whether it be permanently increased to another value (like 15 seconds) or that every failed attempt compounds on the original 10 seconds.
