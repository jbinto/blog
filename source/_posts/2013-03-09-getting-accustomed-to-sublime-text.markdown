---
layout: post
title: "Getting accustomed to Sublime Text"
date: 2013-03-09 12:18
comments: true
categories: sublime-text
---

[Sublime Text](http://www.sublimetext.com/) is very popular in the Ruby community. I've used it a few times, and I could never understand what all the hype was about. Sure, it looks nice: minimalist, nice fonts and colour schemes. But with so many free alternatives out there, what would possess someone to spend $70 on a text editor? It seems it's just Notepad with color.

{% img /images/sublime-text.png Sublime Text in action. %}

I spent an hour or so going through the excellent (and free!) [Perfect Workflow in Sublime Text](http://net.tutsplus.com/articles/news/perfect-workflow-in-sublime-text-free-course/) screencast series. I haven't made it the whole way through yet, but already I'm seeing the value Sublime adds. I highly recommend making your way through the first few screencasts, these are concepts best demonstrated through video. For reference, the [Sublime Text documentation](http://www.sublimetext.com/docs/2/) is good for refreshing yourself on some of these concepts.

Here are some things that popped out to me. 

## Open folder

Dealing with multiple files at once can be a real pain. In heavyweight IDEs like Eclipse, you need to create a project file, and tell it about each file you want to be a part of the project. This project file can get out of sync with what's physically on your hard drive.

In Sublime, you can open a folder directly. Click "File-Open Folder" (on Mac, it's "File-Open") and point it to a folder. On the left-hand side, you see the folder tree.

## Multiple cursors

The killer feature of Sublime Text is multiple cursors. This is a very powerful concept and is unique to Sublime. 

To create a second cursor, hold `CTRL` (`CMD` on Mac) and left-click anywhere else in the document. A second cursor will appear, and any keystrokes you make will affect both cursors. This allows you to manipulate multiple blocks of text at once. You can create as many cursors as you wish. To get rid of the extra cursors, press `ESC`.

## Adding a cursor to the next occurrence of a word

Rather than using the mouse, you can put the cursor inside a word and press `CTRL+D` (or `CMD+D` on Mac). This will select the next occurrence of the word, and create a second cursor at the end of that word. For example, if you had the text:

`The quick brown fox jumps over the lazy dog`

If you selected the first `the`, and pressed CTRL+D, you'd now have both `the`'s selected.

## Adding a cursor to every occurrence of a word

If you want to select all occurrences of a word, press `ALT+F3`  (`CTRL+CMD+G` on Mac).

## Column selection or block selection

In most other editors, you can only select in a straight, left-to-right line. If you had the following text:

{% codeblock %}
The quick brown fox humps over the lazy dog.
The quick brown fox leaps over the lazy dog.
The quick brown fox soars over the lazy dog.
{% endcodeblock %}

If you wanted to change all of the verbs (humps, leaps, soars) at once, you can do a block selection by holding `SHIFT+RIGHTMOUSE` (or just `MIDDLEMOUSE`) and dragging from the top left ("h" in humps) to the bottom-right (last "s" in soars).

## Incremental search within a file

All text editors have a search feature, but Sublime lets you see the search results as you type. This is similar to the `/` key in vim.

Press `CTRL+I` and start typing. The screen will jump around to the search results as you type. You can tap `F3` to cycle through the results.

## The command palette and fuzzy searching

Another killer feature of Sublime Text is the command palette. Every text editor has keyboard shortcuts, but how are you supposed to remember all of them? The command palette lets you search through all of the Sublime commands.

Press `CTRL+SHIFT+P` (or `CMD+SHIFT+P` on Mac) and the command palette appears. Practically every menu option and keyboard shortcut is accessible through here. With fuzzy searching, you really don't need to remember much.

For example, to tell Sublime you're working on a Ruby file, you could press `CTRL+SHIFT+P` and scroll down to `Set Syntax: Ruby`. Fuzzy searching lets you type just `syntax ruby` or even just `ruby`. I think it's a great example of a discoverable user interface, and it reminds me quite a bit of [IntelliSense](http://en.wikipedia.org/wiki/IntelliSense). However, you *do* need to know that the palette exists in the first place. Sublime doesn't really draw your attention to it.

## Goto anything - fuzzy searching on filenames/paths

If you have a big folder open (perhaps a Rails project), press `CTRL+P`. You can use fuzzy searching to find files within your project. If you're looking for the `app/controllers/users_controller.rb` file, just press `CTRL+R` and type `users_c` -- just like the incremental search above, you'll see it flip between files as you type. That is little jarring at first. With the "goto anything" feature, you don't really need the sidebar anymore.

## Goto symbol (method, class, etc) within a file

Sublime has enough smarts to identify class names and method names. Within a code file (e.g. Ruby, CSS), press `CTRL+R` and search for the name of a method, class, or even a CSS declaration. 

## Surrounding text in quotation marks or parentheses

Select a word or phrase and press `"` to surround it with double quotes. 

You can also use `'` for single quotes, `(` for parentheses, and `{` for curly braces. Very neat.

## Closing the current HTML tag

If you're inside an HTML tag, press `ALT+.` (`OPTION+CMD+.` on Mac) to end the current tag.

## Snippets

Depending on which language Sublime identifies your file as, you can use different kinds of *snippets*. Snippets are little bits of code, and you can insert them using a shortcut.

For example, in any text file you can type `lorem<TAB>` and get:

{% codeblock %}
Lorem ipsum dolor sit amet, consectetur adipisicing elit. Nisi debitis officiis ad porro nihil totam quibusdam ab quae quis ipsa quo eveniet corrupti! Incidunt libero dolores adipisci eum omnis earum.
{% endcodeblock %}

What make snippets so impressive is not just a bunch of boilerplate code. The cool part is how you can manipulate the cursor between *fields*. Let's say you're trying to define an entry in a Ruby hash. Normally, you'd type `:key => "value"`. With a snippet, you can type just `:<TAB>` and it inserts the snippet. Ok, cool, but nothing special really.

What's special is that Sublime has pre-selected `:key`. If you press `TAB`, it *jumps over the hashrocket* `=>` and straight to `"value"`. 

So, to get `:option => true` you could type it verbatim, or:

`:<TAB>option<TAB>true`

Sounds trivial, but the 2 or 3 seconds saved add up, and the value only increases with more complex multi-line snippets.

## Conclusion

I've barely scratched the surface of what Sublime can do. Things get even crazier with plugins like [Emmet](http://docs.emmet.io/), which lets you write one-liner CSS3-like expressions that you can `TAB` to expand into an entire document.

So, is it worth the $70? For now, I'll reserve judgement until I've worked on a significant project or two with it. I see the potential, but in any case I'll probably wait until Sublime Text 3 before opening my wallet.

Take a look at the [screencasts](http://net.tutsplus.com/articles/news/perfect-workflow-in-sublime-text-free-course/) and give it a try yourself!