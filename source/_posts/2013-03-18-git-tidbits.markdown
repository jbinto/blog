---
layout: post
title: "Git tidbits"
date: 2013-03-18 19:27
comments: true
categories: git
---

I spent a lot of time last weekend going through screencasts. The [Code School](http://codeschool.com) videos have really great production quality, but my mind is better tuned for the kind of asynchronous learning you can only get from reading.

So, my rule of thumb is to follow along with these screencasts while recording notes. The following is a dump of my notes about Git. Already a few of these tricks have come in handy.

Note: If you're serious about learning Git, I recommend [Git from the Bottom Up (PDF)](http://ftp.newartisans.com/pub/git.from.bottom.up.pdf).  I read that a few months ago, and while it didn't make a whole lot of sense then, I realize now that it has coloured my thoughts in a certain way when I'm actually using Git. 

## Fixing the last commit

`git commit --amend -m "New message"`

Once in a while, you'll commit something, and then realize "that's *not* what I wanted". As a Git novice you might just create another commit with a message like "oops".

Rather than polluting your history, you can use --amend. On the surface (forgive me if my language is imprecise), this reverses the previous commit, and replaces it with what you have now.

Caveat: If you already pushed to a remote like Github, you will have to do `git push --force`, since Git will think you have changes to pull. Be careful with these more dangerous commands; always take a backup.


## Soft and hard resets 

Resets are all about changing history. Some people believe this goes against the principles of source control, but what else are you going to do if you've published sensitive information?

If you know about the Git internals, you'll know all about the HEAD pointer and what it does behind the scenes. Chances are if you're considering using the `reset` command you already know all of this. I think resets are best demonstrated by example. Consider the following scenario:

### Scenario

Consider the following scenario:

{% codeblock %}
$ git init
$ echo "This is foo" > foo
$ echo "This is bar" > bar
# note: git commit -a -m wouldn't work here, because foo and bar are untracked
$ git add .          
$ git commit -m "Add foo and bar"

$ cat *
This is foo
This is bar

# Making a mistake below
$ echo "Bars of gold" >> foo

# note: I can use -a here, because bar is tracked, but not staged
$ git commit -a -m "Add bars of gold to bar"
$ git log    # we see two commits here

# Later I realize: Oops! I wanted to add it to bar!
{% endcodeblock %}

### Fixing it with a soft reset

{% codeblock %}
$ git reset --soft HEAD^   # reset to one before the current, but keep staged changes.
$ git log                  # only one commit. Okay.
$ cat *
This is foo
This is bar
Bars of gold

# The commit was destroyed, but my changes are still in the staging area.
{% endcodeblock %}

### Fixing it with a hard reset

{% codeblock %}
$ git reset --hard HEAD^   # reset to one before the current, and destroy the staged changes
$ git log
$ cat *
This is foo
This is bar
# The commit AND my changes were destroyed.
{% endcodeblock %}

### Fixing it with an amend commit

{% codeblock %}
$ vi foo   # remove it from foo
$ vi bar   # add it to bar
$ git commit --amend -m "Add bars of gold to bar"
{% endcodeblock %}

## Branching

**Create a branch**: `git branch feature`

**Switch to a branch**: `git checkout feature`

**Create a branch and switch to it in one step**: `git checkout -b another-feature`

**Merge an experimental branch into master**

{% codeblock %}
$ git branch          # shows we're on "experimental"
$ git checkout master
$ git merge experimental

# If this is a trivial merge (no changes on master), it uses "fast forward". No commit message necessary.
# If there were simultaneous changes on master and experimental, it uses "recursive". This pops up a vi window, and makes you record the merge.
{% endcodeblock %}

## Pushing and pulling

### Scenario

**Developer A:**

{% codeblock %}
$ git clone SOMEREPO
# Start making some changes....
{% endcodeblock %}

**Developer B, meanwhile:**

{% codeblock %}
$ git commit -m 'frobnicate the widget.rb file'
$ git push
# successfully pushed
{% endcodeblock %}

**Developer A:**

{% codeblock %}
# ....and we're done.
$ git commit -m 'twiddle the knobs.rb file'
$ git push 
# ERROR

$ git pull
# Assume the automatic merge succeeded
$ git push
# Forces you to put in a commit message like "Merge branch 'master' of ....'
# Some people believe these merge commits are ugly, and advise to use "rebase" instead.
{% endcodeblock %}


### Editing the same file - merge conflicts

When DevA and DevB both edit the same file in isolation, the first person who pushed "wins".

The second person now has some work to do. Git will dump a bunch of garbage into his file, showing Dev A's changes vs Dev B's changes. He has to resolve this manually (by editing the crap out of the file, and picking out who wins.)

When he commits, the default message will be "Merge branch 'master' of ...."

### Remote branches

So far, I've only ever seen local branches. For the most part, only the `master` branch ever makes it up to Github. The "branch-work-work-merge-push" pattern. But what if you have a highly experimental branch, which might take a few weeks to work on, while mainline development continues in parallel? You might still want to push those experimental changes up to Github.

For this we need a remote tracking branch.

{% codeblock %}
$ git checkout -b secret-sauce
$ git push origin secret-sauce
{% endcodeblock %}

Now, when another dev pulls, they'll see the new branch is now available, but not locally.

{% codeblock %}
$ git branch
* master

# Hmm... where is secretsauce?
{% endcodeblock %}

{% codeblock %}
# We need to pass -r to see remote branches.
$ git branch -r
  origin/master
  origin/secret-sauce

$ git checkout secret-sauce
# This creates a "remote tracking branch"
{% endcodeblock %}

### Listing remote branches, whether they're tracked or not

`git remote show origin`

### Deleting a remote branch

This stood out as a huge red flag to me. This is not at all intuitive, obvious, consistent, or sensical. But, to delete the remote branch on Github, we do:

`git push origin :secret-sauce`

This begins to make sense later, when we see how to push a local branch to a different remote branch.

### Deleting a local branch

{% codeblock %}
$ git branch -d secret-sauce   # This only deletes if it's been merged somewhere
$ git branch -D secret-sauce   # This is less safe, deletes the branch unconditionally
{% endcodeblock %}

### What if someone else already deleted my remote branch?

{% codeblock %}
$ git push
# Does nothing. He thinks, "WTF?"

$ git remote show origin
.... "stale" (use 'git remote prune' to remove)
# Oh, that branch got merged into master, so now it's stale. 
# Ok, let's clean up my remotes.

$ git remote prune origin
{% endcodeblock %}

### Pushing a local branch to a different remote branch

What if you want to push from local branch "secret-sauce" to remote branch "master" (e.g.  for Heroku, since it only lets you push to master).

{% codeblock %}
$ git push origin secret-sauce:master
# Pushes local branch secret-sauce to remote branch master
{% endcodeblock %}

That was as far as I got. There's still tagging, rebasing, and a few other things to cover at a later date. For now, I have *just* enough Git knowledge to be dangerous.

n.b. Sorry about the brain-dump format of this post. Kudos for making it this far.