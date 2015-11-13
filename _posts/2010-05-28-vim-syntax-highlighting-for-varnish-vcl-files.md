---
title: VIM Syntax Highlighting For Varnish VCL Files
author: Michael Goffin
layout: post
permalink: /2010/05/28/vim-syntax-highlighting-for-varnish-vcl-files/
tt_auto_tweet:
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - http://is.gd/1xx1tg
categories:
  - Varnish
  - VIM
tags:
  - Varnish
  - VIM
---
<a href="http://varnish-cache.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://varnish-cache.org/', 'Varnish']);" >Varnish</a> has become a popular topic of conversation lately, and rightly so. It is amazing. The Varnish-cache project describes it as simply a high-performance HTTP Accelerator. Outside of its use as a caching layer, it is also an excellent load balancer and proxy. 

One of the great things about Varnish is the <a href="http://varnish-cache.org/wiki/VCL" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://varnish-cache.org/wiki/VCL', 'Varnish Configuration Language']);" >Varnish Configuration Language</a>. VCL files define the policies for Varnish when handling requests and for caching and are written in a syntax similar to C and Perl. What makes this so amazing is the following:

> When a new configuration is loaded, the varnishd management process translates the VCL code to C and compiles it to a shared object which is then dynamically linked into the server process. 

This just blows my mind. Talk about going the extra mile to squeeze out as much performance as you can from your application. It makes the VCL syntax much easier to understand when you can think about it in these terms and know what the application is going to do with it. 

I&#8217;ve been sifting through VCL files in my free time to accumulate a list of nifty tricks that I might be interesting in using in the future. <a href="http://www.vim.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.vim.org/', 'VIM']);" >VIM</a> being my editor of choice, I hunted for a syntax file that would help when reading VCL files. I ran across <a href="http://cvs.pld-linux.org/cgi-bin/cvsweb.cgi/packages/vim-syntax-vcl/vcl.vim" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://cvs.pld-linux.org/cgi-bin/cvsweb.cgi/packages/vim-syntax-vcl/vcl.vim', 'one']);" >one</a> written by Elan Ruusamäe and it was exactly what I needed. Download the latest version and put it in your ~/.vim/syntax directory. Then make the following additions to your .vimrc:

<pre>au BufRead,BufNewFile *.vcl :set ft=vcl
au! Syntax vcl source ~/.vim/syntax/vcl.vim
</pre>

Now I can enjoy syntax highlighting when ripping through example code like I would most other things I edit with VIM.

