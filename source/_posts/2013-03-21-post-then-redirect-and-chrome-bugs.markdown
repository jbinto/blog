---
layout: post
title: "POST-then-redirect and Chrome bugs"
date: 2013-03-21 09:53
comments: true
categories: rails http chrome
---

For the past few days I've been working on [my first Rails app](https://github.com/jbinto/photogur). 

A common pattern in web applications is [Post/Redirect/Get](http://en.wikipedia.org/wiki/Post/Redirect/Get). The idea is that if you return any content from a POST, you will break the browser. Consider the following:

* User sends a POST request to /pictures
* /pictures returns some result, like "Thanks for submitting."
* If the user reloads the page, they'll get an ugly "You are resubmitting data" dialog. If they click OK, duplicate data will be submitted.
* If the user bookmarks the page, when they come back, it will be a GET request. This may or may not be what we want.

You see this all the time in older, clunky web applications (think government). If you press the BACK button, it breaks. If you reload, it breaks. And don't even *think* about bookmarking anything.

{% img /images/repost_warning.png A typical re-POST warning. %}

Fortunately, web developers have a pattern to fix this. After completing a `POST`, they will redirect the user to a "safe" page. This redirect is always made using `GET`. Common Rails practice is to always redirect from a `POST` action, like so:

{% codeblock lang:ruby %}
if @picture.save
  redirect_to pictures_path
end
{% endcodeblock %}

But when I pressed reload on the `/pictures` page, I saw that ugly resubmit dialog! It didn't make any sense. I opened the inspector in Chrome, and it showed exactly what I expected: a `POST` to `/pictures`, a redirect, and a `GET` to `/pictures`.

OK, I must be misunderstanding something in Rails. I press reload, blindly click OK, and I see the last `POST` is duplicated! This is strange...

Somehow I get the inclination to look up `chrome reload after 302` (302 is the HTTP status code for redirects). And I find this [Chrome bug report](https://code.google.com/p/chromium/issues/detail?id=177855). It confirms that the behavior I'm seeing is in fact a bug in Chrome 25.

It turns out, sometimes the [compiler *is* broken](http://pragmatictips.com/112).

