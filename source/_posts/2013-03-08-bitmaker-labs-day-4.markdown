---
layout: post
title: "Bitmaker Labs Day 4"
date: 2013-03-08 06:53
comments: true
categories: bitmaker-labs
---

On Thursday, we started a deep dive into Ruby. There's so much about this language to love. 

* **It encourages a functional style of programming.** It's a lot more about telling Ruby *what* to do rather than *how* to do it. 
{% pullquote %}
An example of this is the heavy use of methods like `map`, `select`, and `inject`. In a language like Java, you tell the computer how to do something. For example, you might say, "given an array, create another array. For every element in the first array, if it is less than four, copy it into the new array". This might be 4 to 7 lines of code, most of which is describing *how* to do something. With detailed instructions like this, it's easy to lose sight the big picture. 



But in Ruby, we'd simply say:

{% codeblock Filtering elements from an array in Ruby lang:ruby %}
teams = ["NYI", "NYR", "TOR", "BOS", "MTL", "OTT"]
nyc_teams = teams.select {|team| team.start_with?("NY") }
{% endcodeblock %}

This reads almost like English. If I were to read this aloud, I'd say, "To get a list of NYC teams, from the list of all teams, select each team where the team starts with NY". It's not so clever to be hard read, and it's not so verbose to get bogged down in the details. 

{" Overheard at @bitmakerlabs: If Ruby were a woman, I'd probably divorce my wife.  "}

{% endpullquote %}


* **Punctuation is optional.** This is probably the most surprising aspect of Ruby to me, and I'm not quite sure how I feel about it. 

{% codeblock Punctiation is optional in Ruby lang:ruby %}
say_something("Hi")
say_something "Hi"   # equivalent to above
{% endcodeblock %}
 
* **Everything is object oriented.** In Ruby, even the mathematical operators are methods on an object:

{% codeblock Arithmetic in Ruby is just a method call lang:ruby %}
result = 4 + 5
result = 4.+(5)     # equivalent
{% endcodeblock %}

I'm looking forward to reading more about blocks and lambdas in Ruby, and more about object oriented design. This weekend, we'll be reading Sandi Metz's book [Practical Object-Oriented Design in Ruby](http://www.amazon.com/dp/0321721330). 
