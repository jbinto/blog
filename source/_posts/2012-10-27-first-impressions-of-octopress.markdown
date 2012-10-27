---
layout: post
title: "First impressions of Octopress"
date: 2012-10-27 08:51
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

``` diff sass/_custom/_colors.scss
diff --git a/sass/custom/_colors.scss b/sass/custom/_colors.scss
index 740266a..bc80145 100644
--- a/sass/custom/_colors.scss
+++ b/sass/custom/_colors.scss
@@ -2,12 +2,12 @@
 // To give it a try, uncomment some of the lines below rebuild your blog, and see how it works.
 // If you need a handy color picker try http://hslpicker.com

-//$header-bg: #263347;
+$header-bg: #0f164b;
 //$subtitle-color: lighten($header-bg, 58);
-//$nav-bg: desaturate(lighten(#8fc17a, 18), 5);
+$nav-bg: desaturate(lighten(#80849f, 18), 5);
 //$nav-bg-front: image-url('noise.png');
 //$nav-bg-back: linear-gradient(lighten($nav-bg, 8), $nav-bg, darken($nav-bg, 11));
-//$sidebar-bg: desaturate(#eceff5, 8);
+$sidebar-bg: desaturate(#eceff5, 8);
 //$sidebar-link-color: saturate(#526f9a, 10);
 //$sidebar-link-color-hover: darken(#7ab662, 9);
 //$footer-bg: #ccc !default;
```

