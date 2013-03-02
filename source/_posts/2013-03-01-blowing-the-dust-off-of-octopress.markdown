---
layout: post
title: "Blowing the dust off of Octopress"
date: 2013-03-01 23:18
comments: true
categories: 
---

I've taken the time to refresh myself on my Octopress setup. It's been a few months since I touched it, so obviously I'm a little rusty. 

I've moved everything from Heroku to my static host, which is hosted on <a href="http://linode.com">Linode</a>. I was upset with the long (10s) spin-up times for an inactive Heroku website, and wasn't clear what kind of limitations I'd face with posting large images. I also thought it was a good idea to make this blog the front page of my personal domain, rather than segregating it off to blog.jbinto.ca.

It took a while to get it all set up, but now posting is fairly simple:

```
$ rake new_post["Blowing the dust off of Octopress"]
mkdir -p source/_posts
Creating new post: source/_posts/2013-03-01-test.markdown
```

You open the [Markdown](http://en.wikipedia.org/wiki/Markdown) file in your favourite text editor, and edit that. You can preview using `rake preview` and going to localhost:4000 in your browser. To deploy the blog, do:

```
rake generate
rake deploy
```

I also have the source code for the blog [hosted at Github](http://github.com/jbinto/blog).

Why would I use this over something like Wordpress? It seems so complicated! Well, here are some reasons that come to mind:

## Pros

* I have complete control over the generated HTML
* No dependence on a database server or server-side language
* The data can be under source control, backed up at Github
* The data is in plain text, so it will last forever
* Can be hosted anywhere, since it's just static HTML
* Secure; don't have to worry about upgrading Wordpress
* I get a nice responsive layout for free
* It's written in Ruby

## Cons

* Need access to a terminal, ssh, git to post. 
* Images are tricky. I can embed images directly, but getting nice thumbnails will probably require work. The Flickr feed is nice, but I'll probably need to post screenshots at some point.
