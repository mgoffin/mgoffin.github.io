---
title: Upgrading Gitorious
author: Michael Goffin
layout: post
permalink: /2010/04/27/upgrading-gitorious/
tt_auto_tweet:
  - 'false'
  - 'false'
tt_auto_tweet_text:
  - 'New blog post: [TITLE] [URL]'
  - 'New blog post: [TITLE] [URL]'
tweet_this_url:
  - http://is.gd/OduRs5
categories:
  - Git
  - Ruby
tags:
  - Git
  - Gitorious
  - Redmine
  - Ruby
---
**[2010-07-12] UPDATE:** I just confirmed that this works for the latest mainline.

We use Gitorious at work as our Git front-end. We opted to not use Github since we wanted something we didn&#8217;t have to pay for, and was internal to our network. It has a great feature-set, and pretty much does everything we need it to do and more. 

There is a bit of documentation on how to get Gitorious installed (as painful as it may be), but there&#8217;s little to no documentation on how to upgrade it once it&#8217;s installed. I just went through an upgrade and it turned out to be much easier than I was anticipating. 

First, we need to get the latest source:

<pre>$ cd ~
  $ mkdir ~/gitorious
  $ git clone git://gitorious.org/gitorious/mainline.git gitorious
</pre>

Next, we need to go through and compare all of the configuration files.

<pre>$ cd gitorious
  $ cd config
  $ diff &lt;foo&gt; /path/to/current/gitorious/install/configs/&lt;foo&gt;
</pre>

Where **foo** is any specific configuration file in the new Gitorious source and your current install. I found that for the most part I was able to copy my old configs in place and not have to change anything. 

This step is dependent on your install, but I made a few custom modifications to the source. I did not like how the &#8220;Projects&#8221; and &#8220;Repositories&#8221; pages were not listed alphabetically. They seemed to be ordered by descending creation date. Here&#8217;s the changes if you want to see how to do this as well:

<pre>--- app/views/projects/index.html.erb       2010-04-27 10:57:09.109711812 -0400
+++ /var/www/gitorious/app/views/projects/index.html.erb        2010-04-27 10:29:50.799722064 -0400
@@ -25,7 +25,7 @@
 &lt;h1&gt;Projects&lt;/h1&gt;

 &lt;ul class="project_list"&gt;
-&lt;% @projects.each do |project| -%&gt;
+&lt;% @projects.sort_by { |p| p[:title] }.each do |project| -%&gt;
   &lt;li class="project_list_item"&gt;
     &lt;%= render :partial =&gt; project, :object =&gt; project -%&gt;
   &lt;/li&gt;
--- app/views/projects/show.html.erb        2010-04-27 10:57:09.109711812 -0400
+++ /var/www/gitorious/app/views/projects/show.html.erb 2010-04-27 10:29:50.799722064 -0400
@@ -32,7 +32,7 @@
   &lt;%= render_markdown(@project.description, :auto_link) -%&gt;
 &lt;/div&gt;

-&lt;% @project.repositories.mainlines.each do |repo| -%&gt;
+&lt;% @project.repositories.mainlines.sort_by { |r| r[:name] }.each do |repo| -%&gt;
   &lt;%= render :partial =&gt; "repositories/overview", :locals =&gt; {:repository =&gt; repo} -%&gt;
 &lt;% end -%&gt;
</pre>

We also use Redmine at work for issues and project management within our team. One of the features of Redmine is the ability to browse source and do code reviews. In order to do this, Redmine needs access to the repositories. A lot of solutions out there suggest writing cronjobs to clone the source into a place Redmine can read. I found this horrible since the clone will be out-of-date quickly unless you have a cronjob which runs more frequently than your developers commit. My solution is a bit better, and allows for live-code browsing. We happen to have Redmine on the same system as Gitorious (both being Ruby on Rails, this worked out nicely). In our Redmine install, I created a symlink to the Gitorious **repositories** folder (you can use an NFS mount if they are hosted on different machines). That solved Redmine&#8217;s ability to have direct access to the source. The problem with that is the Gitorious naming-scheme is not human-readable. So the problem we had is how to tell Redmine which folder to look at for a specific project. We solved this by using the directory information in the Gitorious database and exposing it in a format that can be pasted into Redmine. Here&#8217;s the diff for this feature if you want to use it:

<pre>--- app/views/repositories/show.html.erb    2010-04-27 10:57:09.109711812 -0400
+++ /var/www/gitorious/app/views/repositories/show.html.erb     2010-04-27 10:29:50.809717943 -0400
@@ -63,6 +63,9 @@
       &lt;strong&gt;&lt;%= t("views.repos.created") %&gt;:&lt;/strong&gt;
       &lt;%= @repository.created_at.to_s(:short) -%&gt;
     &lt;/li&gt;
+    &lt;li&gt;
+      &lt;strong&gt;Redmine: &lt;/strong&gt;/var/www/redmine/repositories/&lt;%= @repository.real_gitdir.to_s() -%&gt;
+    &lt;/li&gt;
   &lt;/ul&gt;

   &lt;ul class="links"&gt;
</pre>

This will show up in Gitorious in the informational section on the right-hand side when viewing a repository. 

Now that you have copied your configuration files over, and your personalized code changes, it&#8217;s time to put the new source in place. **Backup your current install and current database before doing anything**. Once that is backed up, delete the old install, and put the new one in place. Copy your **repositories** directory from the old install into the new one. Copy your **public/system** directory from your old install to the new one to migrate avatars. Then update the database (this is assuming you are using the production environment):

<pre>$ cd /path/to/new/gitorious/install
  $ rake db:migrate RAILS_ENV=production
</pre>

This shouldn&#8217;t return any errors, just a list of database changes that were made to accommodate the new install. If that is successful, you can restart your services (we use Apache, so it was just a matter of restarting Apache) and then visit your Gitorious page to make sure nothing is horribly broken. If there are any problems, it is most likely an issue with the version of Ruby, or of the gems you have installed. Review the contents of **/path/to/new/gitorious/install/README** for information on what versions are required and how to install them.

<div class="tweetthis" style="text-align:right;">
  <p>
    <a href="http://twitter.com/intent/tweet?text=Upgrading+Gitorious+http%3A%2F%2Fis.gd%2FOduRs5" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://twitter.com/intent/tweet?text=Upgrading+Gitorious+http%3A%2F%2Fis.gd%2FOduRs5', '']);" class="tt"  title="Post to Twitter"><img class="nothumb" src="http://www.mgoff.in/wp-content/plugins/tweet-this/icons/en/twitter/tt-twitter-micro4.png" alt="Post to Twitter" /></a>
  </p>
</div>