# Quigkin - Github Pages

## TLDR

This repo contains the source of [quigkin.com](https://quigkin.com/) which is a [GitHub Pages](https://pages.github.com/) hosted site.

[Jekyll](https://jekyllrb.com/) is used to transform the Markdown to a static site.

## Develop

TODO - Checkout Github's new preview feature

This project uses Docker for locally building and testing the site. Using Docker helps us avoid updates to our dev environment breaking Jekyll and/or it's dependencies. Nothing is worse than expecting to spend 15 minutes on a task and finding yourself hours later tracking down an issue.

*NOTE* This Docker image is not for running the site in production. This is only for local development and generating the static site. 

### docker build

You need to build the image initially and then after changes to your Ruby gem dependencies. You do not need to build it when changing the site.

```
docker build -t='jekyll' .
```

### docker run

You can run the `jekyll` image using the following command.

```
docker run --rm \
  --volume="$PWD:/var/jekyll:Z" \
  --publish 4000:4000 \
  --publish 35729:35729 \
  jekyll
```

The above will run Jekyll with the following options which will poll for changes to site content and cause the browser to automatically reload.
```
jekyll serve -H 0.0.0.0 -P 4000 --force_polling --livereload
```

### docker run ...

You can specfiy a Jekyll command if you want something other than the default behavior; for instance, the following command will have Jekyll check your site for issues.

```
docker run --rm \
  --volume="$PWD:/var/jekyll:Z" \
  jekyll \
  jekyll doctor
```

Use the following to look at the full set of commands and options.

```
docker run --rm \
  --volume="$PWD:/var/jekyll:Z" \
  jekyll \
  jekyll --help
```

Or options for a subcommand.

```
docker run --rm \
  --volume="$PWD:/var/jekyll:Z" \
  jekyll \
  jekyll serve --help
```

## Deploy

