---
layout: post
title: "Git tips to improve your workflow"
description: ""
date: 2017-05-04
tags: [git]
comments: true
share: true
---

Git is a powerful tool, no one questions that. Though, many people still uses
only the basics and never gets to really enjoy it's benefits.

If you're one of those busy people that never get the time to properly study
git, let me help with some simple yet amazing tips.

## Graphical commit history

`git log --graph --oneline --decorate`

If you're a fan of the graphical commit tree of clients such as SourceTree and
GitKraken, you may create a similar one by passing the `--graph` flag to
`git log`. The `--oneline` flag here will omit unecessary information, showing
only the commit's SHA-1 and message - the tree would feel a little bloated
without it. The `--decorate` will highlight the branches refs, making it easy
to localize ourselves.

## Command aliases

`git config --global alias.<alias> <command>`

Git allows us to define aliases for anything we want. For example, we may
want to create an alias to show the commit tree from the previous tip:

`git config --global alias.tree 'log --graph --oneline --decorate'`

Now `git tree` will give us a graphical history of commits.

## History rewrite

`git rebase HEAD~<n> -i`



## Binary search debug
git bisect

## Getting file from another branch
git checkout experiment -- <file>

git cherrypick
