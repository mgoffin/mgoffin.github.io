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
  - https://is.gd/OduRs5
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

We use Gitorious at work as our Git front-end. We opted to not use Github since we wanted something we didn't have to pay for, and was internal to our network. It has a great feature-set, and pretty much does everything we need it to do and more. 

There is a bit of documentation on how to get Gitorious installed (as painful as it may be), but there's little to no documentation on how to upgrade it once it's installed. I just went through an upgrade and it turned out to be much easier than I was anticipating. 

First, we need to get the latest source:

{% highlight bash %}
$ cd ~
$ mkdir ~/gitorious
$ git clone git://gitorious.org/gitorious/mainline.git gitorious
{% endhighlight %}

Next, we need to go through and compare all of the configuration files.

{% highlight bash %}
$ cd gitorious
$ cd config
$ diff <foo> /path/to/current/gitorious/install/configs/<foo>
{% endhighlight %}

Where **foo** is any specific configuration file in the new Gitorious source and your current install. I found that for the most part I was able to copy my old configs in place and not have to change anything. 

This step is dependent on your install, but I made a few custom modifications to the source. I did not like how the &#8220;Projects&#8221; and &#8220;Repositories&#8221; pages were not listed alphabetically. They seemed to be ordered by descending creation date. Here's the changes if you want to see how to do this as well:

{% highlight diff %}
--- app/views/projects/index.html.erb       2010-04-27 10:57:09.109711812 -0400
+++ /var/www/gitorious/app/views/projects/index.html.erb        2010-04-27 10:29:50.799722064 -0400
@@ -25,7 +25,7 @@
 <h1>Projects</h1>

 <ul class="project_list">
-<% @projects.each do |project| -%>
+<% @projects.sort_by { |p| p[:title] }.each do |project| -%>
   <li class="project_list_item">
     <%= render :partial => project, :object => project -%>
   </li>
--- app/views/projects/show.html.erb        2010-04-27 10:57:09.109711812 -0400
+++ /var/www/gitorious/app/views/projects/show.html.erb 2010-04-27 10:29:50.799722064 -0400
@@ -32,7 +32,7 @@
   <%= render_markdown(@project.description, :auto_link) -%>
 </div>

-<% @project.repositories.mainlines.each do |repo| -%>
+<% @project.repositories.mainlines.sort_by { |r| r[:name] }.each do |repo| -%>
   <%= render :partial => "repositories/overview", :locals => {:repository => repo} -%>
 <% end -%>
{% endhighlight %}

We also use Redmine at work for issues and project management within our team. One of the features of Redmine is the ability to browse source and do code reviews. In order to do this, Redmine needs access to the repositories. A lot of solutions out there suggest writing cronjobs to clone the source into a place Redmine can read. I found this horrible since the clone will be out-of-date quickly unless you have a cronjob which runs more frequently than your developers commit. My solution is a bit better, and allows for live-code browsing. We happen to have Redmine on the same system as Gitorious (both being Ruby on Rails, this worked out nicely). In our Redmine install, I created a symlink to the Gitorious **repositories** folder (you can use an NFS mount if they are hosted on different machines). That solved Redmine's ability to have direct access to the source. The problem with that is the Gitorious naming-scheme is not human-readable. So the problem we had is how to tell Redmine which folder to look at for a specific project. We solved this by using the directory information in the Gitorious database and exposing it in a format that can be pasted into Redmine. Here's the diff for this feature if you want to use it:

{% highlight diff %}
--- app/views/repositories/show.html.erb    2010-04-27 10:57:09.109711812 -0400
+++ /var/www/gitorious/app/views/repositories/show.html.erb     2010-04-27 10:29:50.809717943 -0400
@@ -63,6 +63,9 @@
       <strong><%= t("views.repos.created") %>:</strong>
       <%= @repository.created_at.to_s(:short) -%>
     </li>
+    <li>
+      <strong>Redmine: </strong>/var/www/redmine/repositories/<%= @repository.real_gitdir.to_s() -%>
+    </li>
   </ul>

   <ul class="links">
{% endhighlight %}

This will show up in Gitorious in the informational section on the right-hand side when viewing a repository. 

Now that you have copied your configuration files over, and your personalized code changes, it's time to put the new source in place. **Backup your current install and current database before doing anything**. Once that is backed up, delete the old install, and put the new one in place. Copy your **repositories** directory from the old install into the new one. Copy your **public/system** directory from your old install to the new one to migrate avatars. Then update the database (this is assuming you are using the production environment):

{% highlight bash %}
$ cd /path/to/new/gitorious/install
$ rake db:migrate RAILS_ENV=production
{% endhighlight %}

This shouldn't return any errors, just a list of database changes that were made to accommodate the new install. If that is successful, you can restart your services (we use Apache, so it was just a matter of restarting Apache) and then visit your Gitorious page to make sure nothing is horribly broken. If there are any problems, it is most likely an issue with the version of Ruby, or of the gems you have installed. Review the contents of **/path/to/new/gitorious/install/README** for information on what versions are required and how to install them.
