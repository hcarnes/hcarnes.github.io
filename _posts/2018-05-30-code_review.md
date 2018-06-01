---
layout: post
title:      "Code Review: Where Code Meets People"
date:       2018-05-29 15:12:15 -0500
permalink:  code_review
---

When I first started considering learning how to program, I asked a developer friend “what if I contribute some code and unwittingly break something?” The response was “there should be a process in place to prevent that type of thing from happening.” Makes sense. Most fields have some form of this practice: QA, copy editing, fact-checking, and the list goes on. For development, that process is called code review. 

There are a lot of ways to implement code review and there are more benefits to the practice apart from keeping things from breaking. Code review provides a consistent opportunity for people to provide feedback and learn new ways to solve problems. This post will summarize some key code review practices, as well as highlight some underpinning reasons for implementing code reviews for any collaborative programming project.

<img src="https://i.imgur.com/zI6PjtT.png" title="cat" height="350" width="350" class="img-responsive"> 

## GitHub pull request model

Increment magazine has a [cool article](https://increment.com/development/what-its-like-to-be-a-developer-at/) on what it’s like to work at various tech companies, including Digital Ocean, Fastly, Gitlab, Lyft, Rainforest, Sauce Labs, and Slack. The writer interviews senior engineers at each company and asks them about range of topics including how their company practices code review. 

The common thread at each company was using the [GitHub pull request model](https://help.github.com/articles/about-pull-requests/). Through this model, you can not only review the changes before they are merged, but you can also use features like labels, milestones, and assignees, and @mention individual contributors or teams to better organize your work. For example, you can use labels to tag pull requests to help you standardize your workflow. Github even provides some default labels to help you get started.

<img src="https://i.imgur.com/8zQSM0U.png" title="labels" height="250" width="250" class="img-responsive">

Another way to help your team stay on track is by setting milestones that include a due date. By associating pull requests and issues to milestones, you can track progress and prioritize the work that will help you reach your goal. 

## Beyond the pull request

Github pull requests are not the panacea of optimizing your code base. Each team should have a process around how code is reviewed. Gitlab and Lyft have interesting approaches to this process.

**Gitlab implements a more structured approach:**
>Every change goes through review by at least one “maintainer.” We have maintainers for frontend and backend, and if a merge request touches multiple areas it has to be approved by both a frontend and backend maintainer. In order to not create a bottleneck on maintainers, larger changes will often first go through a “reviewer” before going to a maintainer for final approval.

**Lyft uses internal tools to integrate with Slack:**
>By default, anyone at Lyft can approve any change, and only one positive review is required to merge code into master for the next deployment. We also have automated bots dealing with simple syntax and styling issues, enabling developers to focus on higher-level discussions.

## Oh, the humanity!

Yes, code review is about the code. BUT there is a person behind each pull request. In the Increment article, Slack was the only company that mentioned having empathy while reviewing code. For example, if you find yourself asking lots of questions or adding a lot of public comments, it might be better to talk to someone in person. Time won’t always permit this luxury, but it may be a necessary approach in some situations. 

**Slack knows good code review:**
>Code review is required at Slack, even in emergency situations. Some of our [earliest posts to our Engineering Blog](https://slack.engineering/how-about-code-reviews-2695fb10d034) are about good code review!) We also have dedicated channels where engineers can post their pull requests for review.

Here is a quick summary of how to successfully review a pull request:

* Be prompt
* Give useful feedback
* Try to identify blind spots and bugs
* Consider the impact to the API
* Consider adherence to the patterns followed at the company
* Look for test coverage

The practice will look different from team to team, but if you follow the points above, you are on the right track. If a team is skipping code review, they are spending a lot of time putting out fires 🔥. Not to mention, they are missing out on valuable opportunities to collaborate, teach, and improve. 🎯
