---
title: Combine your multiple commits into one commit in git
date: 2022-09-01 14:58:10
categories:
  - [Code,Refactoring]
---
When you use the git in your project, you could face a problem about how to combine your loose commits into one commit.

For Examples, I have the commit log like this.
```
First commit 
Fix1
Fix2
Adjust wording
```
I want to conbine commits of the fix1, fix2 and adjust wording into second commit.  
It looks great and clear.  
```
First commit
Second commit
```

In order to become this result, we can use the git command, rebase.
```
git rebase -i <SHA>
```
- SHA : It means the base commit. In this case, the SHA is First commit.

Then you can use the two commands and adjust your commits.
1. pick :  use commit
2. squash : use commit, but meld into previous commit

### The git-operation gif
{% asset_img "rebase.gif" %}




