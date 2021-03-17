---
title: 'IndentationError: unexpected indent'
author: Michael Goffin
layout: post
permalink: /2010/10/26/indentationerror-unexpected-indent/
tweet_this_url:
  - https://is.gd/u0j73d
categories:
  - Git
  - Python
  - VIM
tags:
  - Git
  - Python
  - VIM
---
Tabs and spaces are code killers, visually and syntactically. Dealing with Python, you might see the error in the title often when sharing code with others. Focusing on <a href="https://www.vim.org" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.vim.org', 'VIM']);" >VIM</a>, people love to setup their rcfile so they get the most out of what VIM has to offer, but also makes their code easy to write and easy to read. Unfortunately, many times it&#8217;s just easy to read for them and no one else. 

Python is very picky about spacing and indentation since it&#8217;s used to determine the flow of your script or program. Miss a tab or series of spaces somewhere and all the sudden you are executing code that&#8217;s not inside the **if** statement you just made, or you&#8217;ll get errors like above because your code just flat out fails. 

When dealing with <a href="https://git-scm.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://git-scm.com/', 'Git']);" >Git</a>, and having several people committing code to a similar script or project, you are bound to run into situations where tabstops and shiftwidths are in conflict. It may look like all of the code lines up visually, but where one person uses spaces, another may have used actual tabs. Then you notice they made over 100 changes to the code you&#8217;re working on, and there&#8217;s tabs and spaces mixed all over the place. You have the following in your **.vimrc**:

{% highlight vim %}
set expandtab
set tabstop=4
set shiftwidth=4
{% endhighlight %}

It would be great to fix all of the tabs and spaces to be the same. Now what? VIM to the rescue! Bare with me, this is a bit complicated. Once you open the file in VIM, execute the following:

{% highlight vim %}
:%retab
{% endhighlight %}

You&#8217;re done, by the way. This uses your settings, and converts all of the tabs to the settings in your **.vimrc**. Now your entire file is standardized as far as your spacing goes and you can say goodbye to your unexpected indent errors. If you find yourself having to do this often, you can bind a key to automagically make the changes for you by adding this into your **.vimrc** (using F2 in this example):

{% highlight vim %}
map <F2> :retab <CR> :wq! <CR>
{% endhighlight %}

This will convert all of the tabs to spaces and save the file for you. Enjoy!
