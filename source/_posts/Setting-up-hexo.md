---
title: Setting up hexo
tags:
  - blog
  - hexo
date: 2018-07-11 14:43:52
updated: 2018-07-11 14:53:00
---


# Setting up on MacOS

## Install hexo globally[^1]

    $ yarn global add hexo-cli

## Generate your `my-blog`

    $ hexo init my-blog
    $ cd my-blog

## Keep server running

    $ hexo server --draft --open

* Browser has to be reloaded on every save[^2](This can bee fixed with `hexo-browsersync` plugin)
* This will need to be restarted when `_config.yml` changes.

## Create new draft

    $ hexo new draft "First post"

## Publish a draft

    $ hexo publish <post-name>

* This simply moves post from `source/_drafts to source/_posts` and changes the `date` meta-tag
* Remember to change `updated` meta-tag when updating a post (LIKE NOW!)

## Delete Hello World post

    $ rm ./source/_posts/hello-world.md

# Customization

## Install cactus theme

* Instructions on [github](https://github.com/probberechts/hexo-theme-cactus)

## Current changes:

```yaml (_config.yml)
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: cactus

theme_config:
  colorscheme: classic
  nav:
    '~home': /
    '~archives': /archives/
    '~projects': http://github.com/brobsc
    '~about': http://brobsc.me
  social_links:
    github: brobsc
```

* '~*' on nav items were needed because cactus auto capitalizes pre-configured links (Home, Archives, About)

## Install hexo-footnotes

    $ yarn add hexo-footnotes

* Footnotes have to be at the end of the page, otherwise formatting is broken

### Edit `_config.yml`

```yaml (_config.yml)
plugins:
  - hexo-footnotes
```

# References

1. https://www.cgmartin.com/2016/01/03/getting-started-with-hexo-blog/
1. https://hexo.io/docs/front-matter.html

[^1]: This ran into a error with sharp package. Will have to reinstall it later for xpdf

