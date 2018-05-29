---
layout: post
title:      "Code Review: Where the Code Meets the People"
date:       2018-06-29 15:12:15 -0500
permalink:  code_review
---

I remember when I first started considering learning how to program, and I asked a developer friend “what if I contribute some code and unwittingly break something?”. The response was “there should be a process in place to prevent that type of thing from happening.” Makes sense. Most fields have some form of this practice: QA, copy editing, fact-checking, and the list goes on. For development, that process is called code review. 

There are a lot of ways to implement code review and there are a lot more benefits to the practice apart from just keeping things from breaking. Code review provides a consistent opportunity for people to provide feedback and learn new ways to solve problems. 

Increment magazine has a [cool article](https://increment.com/development/what-its-like-to-be-a-developer-at/) on what it’s like to work at various tech companies, including Digital Ocean, Fastly, Gitlab, Lyft, Rainforest, Sauce Labs, and Slack. The writer interviews senior engineers at each company and asks them about range of topics including how their company approached the practice of code review. This post will summarize some highlights from this article and highlight some underpinning reasons for implementing consistend code review for any collaborative programming project.

## GitHub Pull Request Model

The common thread at each company was using the [GitHub pull request model](https://help.github.com/articles/about-pull-requests/). Through this model, you can not only review the changes before they are merged, but you can also use features like labels, milestones, and assignees, and @mention individual contributors or teams to better organize your work. For example, you can use labels to tag pull requests to help you standardize your workflow. Github even provides some default labels to help you get started.

<img src="https://i.imgur.com/8zQSM0U.png" title="labels" height="400" width="100" class="img-responsive">

Another way to help your team stay on track is by setting milestones that include a due date. By associating pull requests and issues to milestones, you can track progress and prioritize the work that will help you reach your goal. 

## Beyond the Pull Request

Github pull requests are not the panacea of optimizing your code base. Each team should have a process around how code is reviewed. Gitlab and Lyft have interesting approaches to this process.

**Gitlab implements a more structured approach:**
>Every change goes through review by at least one “maintainer.” We have maintainers for frontend and backend, and if a merge request touches multiple areas it has to be approved by both a frontend and backend maintainer. In order to not create a bottleneck on maintainers, larger changes will often first go through a “reviewer” before going to a maintainer for final approval.

**Lyft uses internal tools to integrate with Slack:**
>By default, anyone at Lyft can approve any change, and only one positive review is required to merge code into master for the next deployment. We also have automated bots dealing with simple syntax and styling issues, enabling developers to focus on higher-level discussions.


<img src="https://i.imgur.com/vwLiiVa.jpg?1" title="observables" height="400" width="400" class="img-responsive">

## Oh, the humanity!

Yes, code review is about the code. BUT there is a person behind each pull request. In the increment article, Slack was the only company that mentioned having empathy while reviewing code. For example, if you find yourself asking lots of questions or adding a lot of public comments, it might be better to talk to someone in person. Time won’t always permit this luxury, but it may be a necessary approach in some situations. 

**Slack knows good code review:**
>Code review is required at Slack, even in emergency situations. Some of their earliest posts to their Engineering Blog are about good code review!) They also have dedicated channels where engineers can post their pull requests for review.

Here is a quick summary of how to successfully review a pull request:

* Be prompt
* Give useful feedback
* Try to identify blind spots and bugs
* Consider the impact to the API
* Consider adherence to the patterns followed at the company
* Look for test coverage

If you are working on a team and you aren’t reviewing code, you are not only missing out on a huge opportunity to save time by preventing errors down the road, but you are also missing an opportunity to build value to your team members by encouraging a culture of collaboration, teaching, and self-improvement. 🎯
