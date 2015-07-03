---
layout: post
title: "Git Basics"
date: 2013-05-15 21:15
comments: true
categories: Git
---
Git是Linus Torvalds领导开发的分布式版本管理系统，也是世界上最流行的分布式版本管理系统。关于Git，这里有一本非常好的学习资料[中文版][progit-chinese]和[英文版][progit-english]。下面是最常用的一些命令。
## Init
```
# init a repository with working tree
git init

# init a repository without working tree
git init --bare
```

## Clone
```
# clone from an existing repository
git clone <uri>

# clone from an existing repository and rename it to 'repo-name'
git clone <uri> <repo-name>
```

## Add
```
# add a file 'file' to staging area
git add <file>

# add all files under directory 'dir' to staging area
git add <dir>/*

# recursively add all files under current diretory to staging area
git add .
```

## Status
```

# get the status of current working tree
git status
```

## Commit
```
# commit changes of the working tree in the staging area to local repository
git commit -m "<some comments>"

# reset the last commit and apply changes of the staging area and then commit it again
git commit --amend
```

## Log
```
# show commit history
git log

# show last <num> history
git log -<num>

# show branching graph with history
git log --graph
```

## Remove
```
# remove file from working tree and mark it as removed in staging area
git rm <file> 
```

## Reset
```
# reverse to 'git add <file>', move the changes of 'file' out of staging area
git reset <file>
```

## Revert
```
# drop all changes of 'file' after last commit
git checkout -- <file>
```	

## Branch
```
# create a branch 'new-branch'
git branch <new-branch>

# switch to a branch 'branch'
git checkout <branch>

# create and switch to a branch 'new-branch'
git checkout -b <new-branch>
```

## Merge
```
# merge branch 'other-branch' to current branch
git merge <other-branch>
```

## Stash
```
# push the current staging area to a stack
git stash

# list the stack
git stash list

# pop the top of stack
git stash pop

# get the 'num'th item in the stack and remove it from the stack
git stash pop stash@{<num>}
```

## Tag
```
# set a simple tag 'tag-name'
git tag <tag-name>

# set a annotated tag 'tag-name'
git tag -a <tag-name> -m "<tag-comment>"

# show all tags
git tag

# delete a tag
git tag -d <tag-name>
```

## Remote
```
# add a remote repository, and set its name 'remote-repo'
git remote add <remote-repo> <remote-repo-uri>

# remove a remote repository 'remote-repo'
git remote rm <remote-repo>

# show remote repositories
git remote

# show detailed information of remote repositories
git remote -v

# rename a remote name
git remote rename <old-remote-name> <new-remote-name>

# set local branch 'local-branch' track remote branch 'remote-branch'
git checkout  <local-branch> --track <remote-repo>/<remote-branch>

# create a new local branch and track remote branch 'remote-branch'
git checkout -b <local-branch> <remote-repo>/<remote-branch>

```

## Push
```
# push local repository branch 'local-branch' to remote repository 'remote-repo' branch 'remote-branch'
git push <remote-reop> <local-branch>:<remote-branch>

# if the local branch and remote branch are of the same name 'branch-name'
git push <remote-repo> <branch-name>

# delete a remote branch 'remote-branch'
git push <remote-repo> :<remote-branch> 
```

## Pull
```
# fetch content of 'remote' and merge it with current branch
git pull <remote>
```

[progit-chinese]: http://git-scm.com/book/zh/
[progit-english]: http://git-scm.com/book
