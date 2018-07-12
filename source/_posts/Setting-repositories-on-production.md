---
title: Setting repositories on production
tags:
  - git
  - remote
  - hooks
  - ssh
  - production
date: 2018-07-11 17:54:00
updated: 2018-07-11 18:01:00
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
#!/usr/bin/env bash
set -e

export GIT_WORK_TREE="/home/bruno/blackrole"
export GIT_DIR="/home/bruno/blackrole.git"

mkdir -p $GIT_WORK_TREE

while read oldrev newrev ref
do
  if [[ $ref =~ .*/master$ ]];
  then
    if [ "$newrev" = "0000000000000000000000000000000000000000" ];
    then
      echo "Branch master has been deleted. Aborting post-receive"
    else
      echo "Branch master received. Deploying..."
      cd $GIT_WORK_TREE
      git clean -df
      git checkout -f
      echo "Running yarn..."
      yarn
      echo "Updating submodules"
      git submodule update --init --recursive
      echo "Running hexo generate..."
      yarn run hexo generate
      echo "Push received and deployed succesfully!"
    fi
  else
    echo "Ref $ref successfully received.  Doing nothing: only the master branch may be deployed on this server."
  fi
done
```

* `set -e` prevents script from continuing with errors
* `mkdir -p $GIT_WORK_TREE` creates work_tree directory
* `yarn run <bin>` allow to run bin on `/node_modules/`

### Make it executable

    $ chmod +x hooks/post-receive

* Don't forget #! variable

# Allow git submodule update --init --recursive on server

## Install keychain and your own user

    $ sudo apt-get install keychain

## Add this to your `~/.{zshenv,bashenv}`

```bash (~/.zshenv)
###########################################################################
# allow $USER to use keys. Only enter once and it will remain enabled till
# you delete it or reboot the server 
###########################################################################
/usr/bin/keychain $HOME/.ssh/id_rsa
source $HOME/.keychain/$HOST-sh
```

* Automatic deploy needs to run `git submodule...` without typing passphrase

## Add server ssh key to your github account

    $ ssh -e none you@remote.test "cat /home/you/.ssh/id_rsa.pub" | pbcopy

* This command pipes the values of id_rsa.pub to your clipboard[^1]
* Paste this value as a new ssh key to your github account
* Deploy keys only work for a single repository[^2]

# Deploying to production from dev environment

## Add new remote

    $ git add remote production you@remote.test:/home/you/repo.git

## Push to production remote

    $ git push production master

## DANGEROUS TRICK TO "FORCE" POST-RECEIVE

### Allow receive.denyDeleteCurrent

```config (repo.git/config)
[receive]
  denyDeleteCurrent = false
```

### Delete remote branch then push again

    $ git push production :master
    $ git push production master

# References

1. https://stackoverflow.com/questions/1030169/easy-way-to-pull-latest-of-all-git-submodules
1. https://stackoverflow.com/questions/15231937/heroku-and-github-at-the-same-time
1. https://stackoverflow.com/questions/13677125/force-git-to-run-post-receive-hook-even-if-everything-is-up-to-date
1. https://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent
1. https://www.cyberciti.biz/faq/ubuntu-debian-linux-server-install-keychain-apt-get-command/
1. https://stackoverflow.com/questions/19692354/git-dont-call-post-receive-hook-when-deleting-a-branch

[^1]: `pbcopy` is for mac only. Use `xsel` or `xclip` on linux
[^2]: You should use a machine account, or, on public repos, simply add submodules with https remote url. See: https://developer.github.com/v3/guides/managing-deploy-keys/
