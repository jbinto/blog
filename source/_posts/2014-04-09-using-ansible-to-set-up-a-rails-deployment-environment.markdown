---
layout: post
title: "Using Ansible to set up a Rails deployment environment"
date: 2014-04-09 9:10:31 -0400
comments: true
categories:
---

I've been using Linux for quite a long time, but I still get a little anxious every time I have to run a server in production, listening on the internet.

It's not so hard to get a server up and running. Even if you're not a system administrator, with a little bit of Linux experience and some Google-fu you can get up and running fairly quickly.

Now, upgrade everything a few times, remove a few packages, add ten more, change the hostname back and forth, break nginx trying to add SSL, install packages from source that overwrite system files. The [jenga tower](https://www.youtube.com/watch?v=I7H6wGy5zf4) is teetering, but somehow it still works.

You have a [snowflake server](http://martinfowler.com/bliki/SnowflakeServer.html).

You're probably not this undisciplined with your production environment. But if you spend most of your time working on software and not infrastructure, and only have one or two servers to manage, you're going to be rusty approximately 100% of the time. When it comes time to change hosts, or if your [server gets compromised](http://heartbleed.com/), are you ready to take action at a moment's notice?

You have to do a lot of things to get a web server running, but none of them are particularly challenging. They are repetitive, detail-oriented, exact. The sort of things humans are lousy at and computers are great at.

Even surgeons can benefit from checklists. Why not create an executable one?

<a href="/images/ansible-postgres.png">{% img /images/ansible-postgres.png 573 368 An Ansible playbook for Postgresql %}</a>

That's exactly what I've done using [Ansible](http://www.ansible.com/).

I've created a [playbook](http://docs.ansible.com/playbooks.html) for provisioning a Rails environment. With **one command**, I can build -- from scratch; not an image -- a brand new virtual server on my laptop with the following:

* Ubuntu 12.04
* Basic server security (SSH keys, [ufw](https://en.wikipedia.org/wiki/Uncomplicated_Firewall), disable `PasswordAuthentication` and `PermitRootLogin`, unattended upgrades)
* Postgresql - latest
* rbenv/ruby 2.1.1
* nginx with Phusion Passenger - latest
* An environment for my Rails app: a `deploy` user, an nginx vhost, a Postgres database/user with a random password, and a `database.yml` pre-set with that password.

The Ansible DSL is easy to understand: it's just a YAML file. It's declarative, like SQL or CSS, not imperative like C or Ruby. It's also idempotent, which means running it many times will not have undesirable side effects. In fact, *running it again on the same server will upgrade it*.

The value of this cannot be overstated. Just like when you first discover the value in test-driven development or source control, there's this feeling of invincibility. Every bit of progress you make is cemented in your playbook.

Having this virtual server "recipe" is great. It means I am always a few minutes away from a reliable Rails environment, even if I lose access to my primary computer. But this is only scratching the surface.

With the `digtal_ocean` Ansible module, I'm able to connect to the [Digital Ocean](https://www.digitalocean.com/) API and spin up cloud instances. I can edit one configuration file, and with **one command**:

* Provision a virtual server on DigitalOcean with the right region/size/SSH keys
* Create an `A` record in DigitalOcean DNS matching the IP I was just assigned

Now I can run the same playbook that worked on my laptop, on the pristine DigitalOcean droplet. Within 10 minutes, I have a freshly-baked Rails environment on the Internet that wasn't there before. And it *just works*.

Once the environment is set up, you can deploy Rails apps  "normally" using Capistrano.

## Why not _____?

* **Heroku:** I'm not Heroku's target customer right now. The free tier is useful when you are certain you can limit yourself to there, but *cost creep* is real. $35 sounds reasonable for a hobby app until it's $70 (another dyno), then $90 (SSL), then $140 (better database)..

I'm trying to work on a PostGIS project, and the lowest tier of database Heroku offers with PostGIS costs $200/month. Not going to happen.

* **Docker/Dokku:** [Docker](https://www.docker.io/) uses some new linux features to provide real containers to applications: think ultra-light weight VMs. [Dokku](https://github.com/progrium/dokku) is an incredible little project that allows Heroku buildpack-style deployments to your own server running docker. It tries to mimic Heroku at every turn, and it's really, really cool.

But it's not really ready for production. At least, not for someone who isn't ready to hack on it a bit. It can be difficult to get containers to talk to each other. The order in which containers start up can be problematic. It seems `dokku` is a small piece of a greater puzzle of a full open source Heroku replacement, which is an upcoming project called [Flynn](http://flynn.io/). Unfortunately I need something now and I'm not willing to work on the bleeding edge.

## Enough talk, show me the code!

Okay. I won't be walking through any of it here, like I normally would. It only took me a few minutes to get the hang of Ansible, the rest of the time was spent trying to get the server configuration just right.

The playbook for building the server (either locally or remotely), and provisioning the DigitalOcean VM, is here:

https://github.com/jbinto/ansible-ubuntu-rails-server

I also took the sample application from Michael Hartl's Rails Tutorial and added a Capistrano 3 configuration to  test a real app deployment.

https://github.com/jbinto/rails4-sample-app-capistrano

If you have any questions or suggestions for improvement, I'd love to hear from you. Leave a comment or send me email at [jbinto@jbinto.ca](mailto:jbinto@jbinto.ca).
