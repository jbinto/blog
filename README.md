# jessebuchanan.ca

This is the source code for jessebuchanan.ca. It is powered by [Octopress 2](http://octopress.org/) and deployed on Amazon S3 / CloudFront using the [s3_website](https://github.com/laurilehmijoki/s3_website) gem. 

## Installation

```
git clone --recursive git@github.com:jbinto/blog.git
cd blog
bundle
```

Set the AWS credentials. **NOTE:** Use a restricted IAM role for this!

```
cp set_aws_credentials.sh.example set_aws_credentials.sh
vi set_aws_credentials.sh
source set_aws_credentials.sh
```
## Upgrading Octopress

See http://octopress.org/docs/updating/

```
git remote add upstream git@github.com:imathis/octopress.git
git fetch upsream
git merge upstream/master
rake update_source
rake update_style
```

Resolve all the merge conflicts and commit.

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

Deployment is handled via `s3_website`. 

```
source set_aws_credentials.sh
s3_website push
```

## Caveats

### source/index.html

I have added my own `source/index.html`, which is a bit of a hack to make the root of the site separate from the Octopress blog. 

Note that `rake update_source` will overwrite `source/index.html`.

I keep a backup in `index.html.jb`. After rebuilding the source (e.g. new theme, Octopress upgrade), run: 

```
cp source/index.html.jb source/index.html
```

### Theme as a submodule

The theme `.themes/boldandblue` is brought in as a git submodule, so I can track it. This brings all of the problems of submodules with it, so be warned.

### JDK required for deployment

Note: `s3_website` requires a JDK as of July 2014, as it is now partially written in Scala.

If you're on OS X and have Homebrew and Cask installed, this is as easy as:

```
brew cask install java
```