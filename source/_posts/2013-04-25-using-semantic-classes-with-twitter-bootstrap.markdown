---
layout: post
title: "Using semantic classes with Twitter Bootstrap"
date: 2013-04-25 09:29
comments: true
categories: 
---

[Twitter Bootstrap](http://twitter.github.io/bootstrap/) is a CSS framework used to provide some commonly used design elements for web projects. It's great to get your project up and running quickly.

I'm not much of a designer, and (most of the time) Bootstrap helps me easily achieve things I'd be hard-pressed to do on my own: well-designed margins & padding, carefully selected colours, and fluid grid layouts that work cross-browser. 

One thing that irks me about Bootstrap, however, is the fact that it litters your markup with their non-semantic class names. If you're not familiar with semantic markup, the idea is that your HTML markup should only indicate *meaning*, not *presentation*. For example:

{% codeblock lang:html Semantic markup compared to non-semantic markup  %}
  <!-- Not semantic: we're saying "this is bold" in the markup. -->
  This is some <b>important</b> text.

  <!-- Not semantic: there's a tag to indicate headers. -->
  <div class="header">Welcome to the site</div>

  <!-- Better: screen-readers can understand the meaning of the strong tag. -->
  This is some <strong>important</strong> text.

  <!-- Better: a web scraper like the Googlebot will understand this is your header. -->
  <header>Welcome to the site</header>
{% endcodeblock %}

However, when you look at Bootstrap's example code, the markup is filled with non-semantic class names:

{% codeblock lang:html A typical Bootstrap example %}
<div class="row">
  <div class="span9">
    Level 1 column
    <div class="row">
      <div class="span6">Level 2</div>
      <div class="span3">Level 2</div>
    </div>
  </div>
</div>
{% endcodeblock %}

I'm [working on](https://github.com/jbinto/puckpicks) an NHL hockey prediction game in Rails, and in it, I want to use some Bootstrap elements:

* Buttons to select the home team by 1, or the away team by 2
* Badges to show the user's score

For instance, if I want to quickly use the green and turquoise buttons, I could just stick `btn btn-success` classes for some buttons (say picking the home team), and `btn btn-info` for others (say, picking the away team). But now I've lost the ability to do things by meaning: I'm not being [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself). If I ever decide the colours should change, I have to search-and-replace on `btn-success` and this may affect other places in my markup.

Luckily, I'm using the [bootstrap-sass](https://github.com/thomas-mcdonald/bootstrap-sass) gem in my project. This allows me to use [SCSS](http://sass-lang.com/) in my project, even though Bootstrap uses the older [LESS framework](http://en.wikipedia.org/wiki/LESS_(stylesheet_language\)).

This means I can make use of the [@extend](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#extend) directive, and keep my class names semantic. So, rather than giving my buttons class names like `btn-success`, I can do the following:

{% codeblock lang:haml HAML markup %}
  - css_class = (team == game.home) ? "home" : "away"
  - css_class += (spread_wager == 1) ? "by1" : "by2"
  = f.submit "#{team.code} +#{spread_wager}", class: "#{css_class}"
{% endcodeblock %}

{% codeblock lang:css SCSS styles %}
input.homeby1, input.homeby2 {
  @extend .btn;
  @extend .btn-success;
}

input.awayby1, input.awayby2 {
  @extend .btn;
  @extend .btn-info;
}
{% endcodeblock %}

Now, my markup has no Bootstrap classes in it. It looks like:

{% codeblock lang:html %}
<input class="awayby2" name="commit" type="submit" value="PIT +2" />
{% endcodeblock %}

Cool! I did the same thing with [badges](http://twitter.github.io/bootstrap/components.html#labels-badges) elsewhere in the project. To show the impact of a user's pick, I apply either the `win` or `loss` class, and `@extend` the Bootstrap badge classes in my SCSS.

[Some people believe this isn't worth it](http://stackoverflow.com/a/12756652/19779), and when it comes to structural/grid layout classes, maybe they're right. I still think it's a good idea to separate my "home team by 1" buttons from my "create a new user" buttons, even if they both have the same `btn-success` class in the end. It will make things easier to change in the future, and won't require me to apply a second CSS class if I ever need to do more.

For my next project I'm going to take a closer look at [Foundation](http://foundation.zurb.com/), a competing CSS framework. This framework seems to have native support for Sass, a first-party Rails gem, and their [documentation](http://foundation.zurb.com/docs/) treats using mixins as a first-class citizen.