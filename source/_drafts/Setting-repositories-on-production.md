---
title: Setting repositories on production
tags:
  - git
  - remote
  - hooks
  - ssh
  - production
---

# Setting up remote tracker

## ssh into your server

    $ ssh you@remote.test

## Create repo.git folder

    $ mdkir ~/repo.git
    $ cd ~/repo.git

## Make it a bare repository

    $ git init --bare

## Create a new `post-receive` hook

```bash (hooks/post-receive)

```

* Don't forget #! variable

### Make it executable

    $ chmod +x hooks/post-receive


# References

1. https://stackoverflow.com/questions/1030169/easy-way-to-pull-latest-of-all-git-submodules
