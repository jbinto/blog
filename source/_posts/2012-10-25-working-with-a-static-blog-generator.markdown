---
layout: post
title: "Working with a static blog generator"
date: 2012-10-25 20:42
comments: true
categories: [tech]
---

I am using [Octopress](http://octopress.org/). This is a little different from other blog software.
Rather than being generated on the fly, and dependent on a database and application code, this
is a "static blog". I write and manage actual "post" files, and I run a command to generate the 
HTML, CSS, the whole site. It's also a [Git](http://git-scm.com/) repository, so I have the whole thing under source
control. And since it's a Git repository, I can push it to [Heroku](http://www.heroku.com/), which is hosting this content.

It will take some getting used to, but it's a neat approach. It is absolutely more secure than a typical
Wordpress installation. It can be hosted for free or nearly free almost anywhere, since it's just static HTML.
It will be lightning fast, since it's just static HTML.

It's also an extreme approach: SSH keys, git repos... if I wasn't already well-versed in this, I would probably be frustrated.

Things I'm not so sure how to do, and should find out:

* Edit the CSS and make it feel different. It uses [responsive design](http://en.wikipedia.org/wiki/Responsive_web_design), which I'm not sure I'll comprehend right away.
* How to embed images (where should they be hosted?)


