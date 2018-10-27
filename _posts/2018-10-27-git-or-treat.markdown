---
layout: post
title:      "Git or Treat"
date:       2018-10-27 00:02:15 -0500
permalink:  git_or_treat
---

Git gets a lot easier the more you just dig in and use it. I feel like there's an inflection point on learning anything, where you can move on from just the basics fundamentals of understanding something to understanding something so well that you can start to think more creatively while using the new skill. For example, think of each Git commits as having a friendly ghost that you can conjure up to learn about this commits past.

I want to share a couple of tricks 🎃 I've learned on how to troubleshoot code problems. If you are just learning Git or if you already have they basics down, I think it would be helpful to be familiar with these Git "tricks".

##  Trick 1 - Sifting through old commits  

If you are working with an unfamiliar codebase, it's helpful to be able to look back at these ghosts and see what changed and how it changed.

* `git log` lists the commits made in that repository.
* `git log --pretty=oneline` keeps each message to one line for readability.
* `git log -p` also shows the 'patch' of code that was changed.
* `git show <commit hash>` show the changes for a specific commit.

What if there is there is a particular file that you want to learn more about? 

* `git log --follow <file path>` shows the commit history for a file.
* `git log -p --follow <file path>` same, but also shows code changed.


## Trick 2 - Searching for more specific things

If you are getting error related to a specific phrase, you can search the codebase for that phrase.

* `git grep <search phrase>` will print lines (with their associate filed) that matches the pattern you search for.

> Did you know grep stands for 'Global regular expression print'? In Regex, the g in g modifier stands for global because it's used to find all matches rather than just the first one.

If you are working in an unfamiliar code base, it's often helpful to know who has also made changes and when those changes were made. 

* `git blame <file path>` shows line by line who changed a file and when it was changed.

You can also alter the output with various command options. This command helps with learning the history of a particular file. So, don't forget the file path!  If you want to know when a file was originally added, it may be easier to use the `git log` command previously discussed followed by the handy dandy `-S` option.

* `git log -S <search phrase>'` shows each commit with that involves your search phrase. This will list all the commits in your repository related to your search phrase.


These commands are a good starter pack for learning on how to get the most information you can out of your old commit. It's definitely reading the docs on a specific command, if there is a specific type of information your are looking for but haven't quite found it.


