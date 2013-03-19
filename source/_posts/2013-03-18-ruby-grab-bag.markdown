---
layout: post
title: "Ruby grab bag"
date: 2013-03-18 19:30
comments: true
categories: ruby
---

I have a large queue of notes I'd like to publish, and in the spirit of my previous [Git tidbits](http://jbinto.ca/2013/git-tidbits/) post, here is a grab bag of Ruby related material I've stumbled upon over the past week or two.

## Boolean evaluation and "truthiness"

In Ruby, there is the concept of "truthiness". Everything is true except `false` and `nil`. This is familiar to anyone with a background in a C-like language.

But in every other language I've tried, all boolean expressions evaluate to `true` or `false`. Not so in Ruby. 

What do you think `"foo" && "bar"` returns? 

Are you sure? 

I'd have said `true`, but apparently it's `"bar"`. 

Ruby methods (and expressions) will return the last expression evaluated. Those values have a truthiness to them, so they can be used in conditionals, but they are *not* evaluated into a boolean like they would be in a C-like language.

## Namespacing

To namespace classes in Ruby, you just nest them. It's important to create your own namespaces, so that you don't [pollute the global namespace](https://www.google.ca/search?q=polluting+the+global+namespace). That is, you don't want your class names clashing with those written by others. It's good practice to have a namespace like `MyProject` and have all of your other classes within that.

For example:

{% codeblock lang:ruby %}
class Foo
  class Error < StandardError
  end

  class Bar
    class Error < StandardError
    end
  end
end

# We can now refer to Foo::Error and Foo::Bar::Error
{% endcodeblock %}

## Convenience methods based on types

The `Exception.new` initializer takes either a `String`, or an `Exception`. This seems common practice in Ruby: providing convenience methods for different types. It's not *quite* [method overloading](http://en.wikipedia.org/wiki/Function_overloading) like in C# or Java (since there's still only one method). I could be wrong, but under the covers it's likely implemented using `if param.is_a?(String)`.

## Inline rescue

Consider `4/0 rescue nil`. This raises a `DivisionByZero` error, which is immediately rescued. The `nil` says "don't do anything with it."

Maybe a more convincing example might be something like this:

{% codeblock lang:ruby %}
items.each do { |item| process(item) rescue next }
{% endcodeblock %}

The above says "we want to call `process` on all `items`, but ignore any exceptions raised." Swallowing exceptions like this isn't great practice, but it's important to understand these Ruby shorthands to see how the language ticks.

## Operator precedence: do/end vs curly braces { }

While writing an innocent unit test to ensure an exception was raised, I got stuck for almost an hour wondering why I couldn't shorten the following code:

{% codeblock lang:ruby %}
assert_raise ZeroDivisionError do
	Calculator.divide(9, 0)
end
{% endcodeblock %}

Into this:

{% codeblock lang:ruby %}
assert_raise ZeroDivisionError { Calculator.divide(9, 0) }
{% endcodeblock %}

I get the following error:
`NoMethodError: undefined method 'ZeroDivisionError' for #<CalculatorTest:0x00000001ff9af0`

[It turns out that Ruby's optional punctuation is in some cases not all that optional.](http://stackoverflow.com/questions/5587264/do-end-vs-curly-braces-for-blocks-in-ruby) It turns out the code I *wanted* to call (the do/end variant above) is:

`assert_raise(ZeroDivisionError) { ... }`

That is, call assert_raise and pass it a class, and a block. But with the parentheses around `ZeroDivisionError` omitted, the curly braces had a higher precedence than the do/end. So I ended up actually calling:

`assert_raise( ZeroDivisionError { ... } )`

Which means "call the `ZeroDivisionError` 'method' (!) with a block, and pass its result to assert_raise". Of course, `ZeroDivisionError` is a class, not a method, so we get the error.

Lesson learned: when in doubt, ask yourself whether you're misunderstanding a Ruby shortcut. Write it out the long way.