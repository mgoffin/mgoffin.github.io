---
title: Writing Files From GridFS To An Archive Without Writing To Disk First
author: Michael Goffin
layout: post
permalink: /2011/07/20/writing-files-from-gridfs-to-an-archive-without-writing-to-disk-first/
tweet_this_url:
  - http://is.gd/JflBab
categories:
  - GridFS
  - MongoDB
  - Python
tags:
  - GridFS
  - MongoDB
  - Python
---
I came across a situation where I needed to generate a <a href="http://bzip.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://bzip.org/', 'bz2']);" >bz2</a> compressed archive of a bunch of files extracted from <a href="http://www.mongodb.org/display/DOCS/GridFS" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/GridFS', 'GridFS']);" >GridFS</a>. This process is going to occur regularly, so I had to take into consideration performance hits against the server. I felt it would be best if I could take the data as it is extracted from GridFS and write it directly to the compressed archive, instead of writing each file to disk first, and then adding it to the archive. 

<a href="http://www.python.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.python.org/', 'Python']);" >Python</a> has a library for generating tarballs called <a href="http://docs.python.org/library/tarfile.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.python.org/library/tarfile.html', 'tarfile']);" >tarfile</a>. This was actually very useful since you can write files directly to a bz2 compressed archive. The issue I ran into is that in order for the data coming out of GridFS to be written to the archive, it had to be written as if it were a file (with file attributes). Using any sort of file IO would force me to write to disk, and that&#8217;s not what I wanted to do. 

Luckily there is <a href="http://docs.python.org/library/stringio.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.python.org/library/stringio.html', 'StringIO']);" >StringIO</a>. Using this in combination with tarfile&#8217;s <a href="http://docs.python.org/library/tarfile.html#tarinfo-objects" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.python.org/library/tarfile.html#tarinfo-objects', 'TarInfo']);" >TarInfo</a> object, this became a very easy task to accomplish:

<pre>import tarfile
import time
from StringIO import StringIO

tar = tarfile.open("sometarfile.tar.bz2", "w:bz2")
for file in gridfs_files:
    info = tarfile.TarInfo(name="%s" % file.name)
    info.mtime = time.time()
    info.size = len(file.data)
    tar.addfile(info, StringIO(file.data))
tar.close()
</pre>

The basic idea is you use TarInfo to specify the filename, size, modified time (this is important otherwise tar will complain when the date is older than <a href="http://en.wikipedia.org/wiki/Epoch_(reference_date)" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://en.wikipedia.org/wiki/Epoch_(reference_date)', 'epoch']);" >epoch</a>), etc. You use StringIO to turn your data into an object tarfile will accept, and you use the two to add the file to the archive. This works really well, except for one issue that I am still working on. If you bunzip2 the compressed archive, and then attempt to do a **tar -t** on it, it hangs and does nothing. It&#8217;s possible that gnu tar has a problem, or that the way tarfile is creating the file isn&#8217;t correct, but it does decompress and explode properly which is the important part!

<div class="tweetthis" style="text-align:right;">
  <p>
    <a href="http://twitter.com/intent/tweet?text=Writing+Files+From+GridFS+To+An+Archive+Without+Writing+To+Disk+First+http%3A%2F%2Fis.gd%2FJflBab" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://twitter.com/intent/tweet?text=Writing+Files+From+GridFS+To+An+Archive+Without+Writing+To+Disk+First+http%3A%2F%2Fis.gd%2FJflBab', '']);" class="tt"  title="Post to Twitter"><img class="nothumb" src="http://www.mgoff.in/wp-content/plugins/tweet-this/icons/en/twitter/tt-twitter-micro4.png" alt="Post to Twitter" /></a>
  </p>
</div>