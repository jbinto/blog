---
layout: post
title: "Bitmaker Labs Day 1"
date: 2013-03-04 17:35
comments: true
categories: bitmaker-labs
---

{% img /images/bitmaker-day1.jpg Bitmaker Labs class %}

Today was the first day of the spring 2013 cohort at [Bitmaker Labs](http://bitmakerlabs.com). I've been waiting for this day for the past 2 months, and it certainly didn't disappoint. There's a great group of people here with very diverse backgrounds (certainly far more impressive than mine!). I think I'm the only one who's worked full-time as a software developer. I'm happy to be here with people who are excited to learn, the enthusiasm is contagious and keeps me going.

The day started with a presentation selling everyone on the idea of working as a software developer, and a tour of "startup culture". This was followed by a live-coding session to review HTML and CSS. We spent the rest of the day in pairs working through a [CodeSchool](http://codeschool.com) module. 

While HTML is a bit of review for me, CSS has always been one of my biggest weaknesses. Going through the [CSS Cross Country](http://www.codeschool.com/courses/css-cross-country) course has been a great help. Here are some things I've learned so far:

First, just a simple shorthand for the **padding/margin properties**:

{% codeblock CSS shorthand for padding/margin lang:css %}
padding: 2px 4px;   /* sets the left/right padding to 2px, top/bottom to 4px */
padding: 1px 2px 3px 4px;   /* TRBL: top right bottom left - "trouble" */
{% endcodeblock %}

Second, **element positioning**. It's really hard to describe without drawing diagrams (I'll have to figure out how to get images with thumbnails in Octopress!), but let's try anyway:

* Consider a page with a single square `<div>` tag. If you set its `position` to `absolute`, you can say `bottom: 0px;` and it will go to the bottom of the page.

* Now, consider nested `<div>`s: a big outer square and a small inner square. Let's say you want to position the inner square at the bottom of the outer square. If you use the strategy above, it will just position it at the bottom of the *page* (as in the first case), not at the bottom of the div. 

* The solution is to set `position: relative;` on the outer div. But why does this work? The "aha" moment for me was realizing that the default `position` is `static`. When absolutely positioning an element, if its parent container is  `static` (and all its parent containers are too), then you're positioning relative to the window. If it's anything other than `static`, you're positioning relative to that container.

Yeah, that was tough to explain.

Third, **clearing floats**. If you float an element left or right, you take it out of the flow of the page. If you have a float nested in a container, the float may overflow the height of that container. The container won't have it's background extended to contain the float. You fix this by clearing the float. There's two ways to do this:

* Creating an empty `<div>` with `clear: both;` as the last element of the container
* Using the [clearfix](http://stackoverflow.com/questions/211383/which-method-of-clearfix-is-best)

It touches briefly on [CSS sprites](http://en.wikipedia.org/wiki/Sprite_\(computer_graphics\)#Sprites_by_CSS), which is a technique for improving performance by reducing the number of small icon-sized images that need to be requested.

Tonight, I'll be working a little more on my [resume](http://jbinto.ca/resume/). At the very least, I want to fix the background image so that it's got a [full page background](http://css-tricks.com/perfect-full-page-background-image/), right now if you zoom out enough you'll see the background repeat, which is ugly. Sometime further down the road, I want to incorporate [responsive design](http://en.wikipedia.org/wiki/Responsive_web_design) into the mix; I'd love to see it display nicely on a phone without resorting to zooming. 
