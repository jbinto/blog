# jbinto.ca

This is the source code for jbinto.ca, the personal website for Jesse Buchanan. It is powered by [Octopress](http://octopress.org/). 

## Installation

```
git clone git@github.com:jbinto/blog.git
cd blog
bundle
```

## New posts

```
bundle exec rake new_post["Lorem ipsum"]
subl source/posts
```

## Building

```
bundle exec rake generate
```

## Previewing

Using [pow](http://pow.cx/) & [powder](https://github.com/Rodreegez/powder):

```
powder link
bundle exec rake watch		# starts Compass to monitor for changes
open http://blog.dev
```

Without pow:

```
bundle exec rake preview
open http://localhost:5000
```

## Deploying

Edit _config.yml to configure deployment.

```
git commit -A
git push origin master
bundle exec rake deploy
```

