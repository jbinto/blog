---
layout: post
title: "Multiple forms in Rails"
date: 2013-04-03 08:45
comments: true
categories: rails
---

Recently I've been going through a few [Railscasts](http://railscasts.com). These videos are great: focused only on a single task, and easy to follow. Last night I was going through [Railscast #136 - jQuery & Ajax](http://railscasts.com/episodes/136-jquery-ajax-revised), and I decided as an exercise in Rails I'd try to implement the non-AJAX form rather than copy-pasting it. It looks something like this:

 {% img /images/tasks.png The tasks app, before AJAX %}

Now for a little background: my previous web development experience has been in ASP.NET (aka "web forms"). This is an attempt to bring a Visual Basic style event driven programming model to the web. It's great for making quick demo apps, but it's a leaky abstraction that falls apart quickly when trying to do anything that doesn't fit their model exactly.

One of the biggest flaws of ASP.NET is the fact that it's postback model forces you to have **only one form per page**. After working this way for a few years, I won't say it became *natural* (because it most certainly isn't), but I learned to live with it.

As soon as I saw how the task page was laid out (with multiple submit buttons), I knew I wouldn't be creating a single form and walking through it looking for changes; rather, one form per task. *(n.b. I realize isn't the best user experience, of course, and that's what the Railscast is all about. I just wanted to make it work exactly as in the example.)*

{% codeblock lang:erb %}
<% @incomplete.each do |task| %>
  <li>
    <%= form_for task do |f| %>
      <%= f.check_box :completed %>
      <%= f.label :completed %>

      <%= f.submit 'Update' %>
      <%= task.task %>
    <% end %>
  </li>
<% end %>
{% endcodeblock %}

This works just fine in terms of submitting the form. Each checkbox/update button pair gets it's own form. However, there's something wrong: the `<label>` tag is broken. Clicking on any label always checks the first box!

Looking into the rendered HTML, I see this:

{% codeblock lang:html %}
<input id="task_completed" name="task[completed]" type="checkbox" value="1" />
<label for="task_completed">Completed</label>
...
<input id="task_completed" name="task[completed]" type="checkbox" value="1" />
<label for="task_completed">Completed</label>
{% endcodeblock %}

There's definitely an issue with this. You can't have multiple HTML elements with the same ID.

The solution is to add a `namespace` to the `form_for` helper:

{% codeblock lang:erb %}
<%= form_for task, namespace: task.id do |f| %>
{% endcodeblock %}

Now the rendered HTML looks like this:

{% codeblock lang:html %}
<input id="5_task_completed" name="task[completed]" type="checkbox" value="1" />
<label for="5_task_completed">Completed</label>
...
<input checked="checked" id="3_task_completed" name="task[completed]" type="checkbox" value="1" />
<label for="3_task_completed">Completed</label>
{% endcodeblock %}

Much better! Every ID on the page is unique and the `<label>` tags work as expected.