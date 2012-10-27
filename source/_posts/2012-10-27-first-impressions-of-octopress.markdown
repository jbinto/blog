---
layout: post
title: "First impressions of Octopress"
date: 2012-10-27 14:35
comments: true
categories: tech
---

It's interesting. It shows I still have a lot to learn. I don't need to know how
everything works exactly: I can treat it as a black box and still be productive.
But here's some things I need to work on:

* CSS 3 including media queries, responsive design
* Markdown: I know how to make links and lists, but that's about it.
* Sass: A CSS compiler to make things [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself)

## How I updated the CSS

I've never been very good at CSS. CSS3 with Sass is almost completely foreign to
me. Since responsive design is all the rage these days, I'll resist my
temptation to rip it all out and start from scratch, and gradually obtain an
understanding of it over time. In the meantime, the Sass is structured in a way
that lets you set customizations (colors, fonts) in a centralized place.

I'm just poking around at these values. ```desaturate``` and ```lighten``` are
Sass functions. 

``` css sass/_custom/_colors.scss
$header-bg: #0f164b;
$nav-bg: desaturate(lighten(#80849f, 18), 5);
$sidebar-bg: desaturate(#eceff5, 8);
```

``` css sass/_custom/_styles.scss
#content {
   font-size: 87.5%;
}

header {
  font-size: 68%;
  text-decoration: underline;
}

h2 {
  color: #93A1A1;
}

/* The nav seems like a waste of space to me. */
nav { display: none; }

/* Everything is too big... */
pre { font-size: 75%; }
```


