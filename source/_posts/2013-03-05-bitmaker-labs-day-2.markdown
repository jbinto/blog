---
layout: post
title: "Bitmaker Labs Day 2"
date: 2013-03-05 20:39
comments: true
categories: bitmaker-labs
---

{% img /images/portfolio-blurred.jpg A rough draft of my portfolio %}

I want to keep up the momentum of blogging my experiences. So I'm writing on the subway after a tiring but fun 13 hour day of learning. I believe reflecting on what I've learned every day is going to be valuable, but it's tough to find the time to write. Writing on the subway is a good way of [timeboxing](http://en.wikipedia.org/wiki/Timeboxing) myself.

So, I'll limit my writing to some tricks I learned today:

Vertical alignment is the bane of all CSS users. But there's a trick for aligning text in a box that's easy enough:

{% codeblock Vertically centering text within a box lang:css %}
div.foo {
  height: 40px;
  line-height: 40px;
}
{% endcodeblock %}

There's nothing better to get people excited about programming than *making something on the screen move*. We covered CSS3 transitions, and watching a box rotate and change colour was received with hoots and hollers.

{% codeblock CSS3 transitions lang:css %}
div {
  background-color: blue;

  transition-property: all;
  transition-duration: 2s;
  transition-timing-function: linear;
}

div:hover {
  background-color: red;
  transform: scale(1.5);
  transform: rotate(360deg);
}
{% endcodeblock %}

The important part to remember is that CSS3 transitions occur when an element's state changes. This happens internally within the browser; calling it an "event" is a bit of a misnomer. I asked "when would this be useful other than for :hover", and the idea is that your JavaScript code might dynamically apply a CSS class to an element. The transition property can help you "calm down" abrupt changes. It intelligently [tweens](http://en.wikipedia.org/wiki/Tweening) the frames in between the beginning and end state.

And last but not least were pseudo-classes. In my previous experience with CSS, I'd only encountered the `a` pseudoclasses: `:visited`, `:active`, etc. But there's a plethora of useful CSS3 pseudo-classes. What if you wanted to:

* Alternate the background color for every other row in a table
* Make all of the text in the first column of a table red

In the old days, you'd have to apply a CSS class by hand to every other row, and a different class for the first column in a table. But with CSS3 pseudoclasses, you can do the following:

{% codeblock CSS3 pseudoselectors lang:css %}
tbody tr:nth-child(odd) { background-color: gray; } 
tr td:first-child { color: red; }
{% endcodeblock %}

I stayed a few hours after class to work on an HTML/CSS portfolio. I'm really happy with what I was able to make in a few hours, but it's not quite ready for primetime, thus the blurry picture. All in due time.
