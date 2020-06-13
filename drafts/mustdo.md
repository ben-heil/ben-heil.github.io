---
layout: post
title: How I Fail to Write Useful Code
subtitle: Reproducible Research for Biologists Who Code Part 1: Must Do's
---
# Overview
A genomicist friend of mine asked me what the best way to make sure code used for biological analyses would be useful for other people.
I started to respond, then realized the subject was way to big to explain clearly with a phone keyboard and might be helpful outside of just her case.
Hence, this blog post.

I plan on splitting the subject into three categories: things one must do, things one should do, and things that are nice to do.
This first post will focus on the "Must do" category.

I did an undergrad in a CS program, and am two years into a computational biology PhD.
The past few years have been enough time to make a ton of mistakes, which will inform these posts.
That being said, this list is likely not comprehensive or entirely correct.
If you disagree or have something to add, feel free to DM me on twitter or email me to tell me about how I'm wrong.

# Must Do's
## Introduction
To do reproducible research it is helpful to think about your code as a part of your methods section.
There are a lot of small details that if omitted make your research difficult or impossible for others to reproduce.
Unfortunately it isn't immediately obvious what several of these are.
The following sections consist of various ways that I have failed to write useful code shared with you so you can avoid the same fate.

## Version Control
Version control is listed first, because it is the first thing to do in any project.
When creating the folder that contains your project, initialize a [version control system](https://www.atlassian.com/git/tutorials/what-is-version-control) such as [git](https://www.atlassian.com/git/tutorials/install-git).
In computational projects version control acts a lot like a lab notebook.
If you are making a [commit](https://www.git-tower.com/learn/git/commands/git-commit) each time you add a feature or an analysis, you end up with a record of what you did and when you did it.

I have wasted a lot of time in the past by not using version control in small projects.
I will frequently make a mistake that breaks everything, or not remember how to do something in code that I deleted.
If I spent the few extra seconds to commit my code, I could have saved hours of time rewriting things.

From a reproducibility perspective, version control lets people run the same code you ran.
Even if you change your code later, you can specify a timestamp or commit to show people the exact analysis you ran.

### Further Reading
Atlassian has good tutorials for [how to set up and use git as a version control system.](https://www.atlassian.com/git/tutorials/setting-up-a-repository)

Hosting your code in the cloud leads to easier backups and sharing.
[GitHub](github.com), [GitLab](https://about.gitlab.com/), and [BitBucket](https://bitbucket.org/product) are all good ways to do this.
I personally use Github, and a tutorial for how to do so is [here](https://guides.github.com/activities/hello-world/).

If you aren't comfortable with the command line GitHub [has an app](https://desktop.github.com/) that keeps you from having to use it.

## Data Access


## Libraries/Packages/Modules

## Random seeds

## Manual steps

## README

## Publish your code
