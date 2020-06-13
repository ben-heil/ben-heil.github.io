---
layout: post
title: How I Fail to Write Useful Code
subtitle: Reproducible Research for Biologists Who Code Part 1: Must Dos
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

# Must Dos
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

## Recording Data Access
Keeping track of which data you use is surprisingly difficult in biological projects.
In an idea world you would include the data that you used with your code, but large datasets and patient privacy make doing so difficult in many cases.
Every case is different, and I don't have a single answer for the best way to make your data available.
Instead, this section covers a number of options and considerations to think about.

### Programmatically Accessing Data
When working with publicly available data, you will likely have a website or other easy interface to download your data.
I encourage you not to use it.

If your dataset is contained in a single file, it is better to write a simple script like the one below to download it.
By writing a download script instead of manually writing out instructions for how to find the file, you can ensure that someone reproducing your work has the same data.

```bash
#!/bin/bash

curl <URL of the data file> -o <location you want the data to be saved to>
```

Datasets are often not a single file though.
Not to fear, the websites providing such datasets typically have a way to interact with them programmatically!
For example, the NCBI has an [API for accessing assorted genetic information](https://www.ncbi.nlm.nih.gov/books/NBK25501/).

Taking the time to learn how to access data programmatically is a valuable investment.
I've accidentally deleted entire directories on more than one occasion.
Having my code backed up and a method to redownload data has led that to be a setback of a couple hours instead of a couple weeks.

### Private Data
Due to patient privacy regulations, you may generate data that you can't make publicly available.
In these cases it is important to document in your [README file](#readme) precisely what data you used, when you accessed it.
Failing to do so makes it difficult or impossible for others to reproduce your analysis.

### Large Datasets
It can be difficult to share data with others when you have generated a large dataset.
[Zenodo](https://zenodo.org/) is the best way I know of.
It's a free large datastore hosted by CERN explicitly for researchers to share code and data.
You can also [get a DOI](https://guides.lib.berkeley.edu/citeyourcode#:~:text=Getting%20a%20DOI%20for%20your%20software%3A&text=Select%20a%20repository%20you%20want,the%20top%20of%20the%20page.) for your dataset and code, allowing others to cite your dataset even if it doesn't have an associated paper.

[Git Large File Storage](https://git-lfs.github.com/) can also be used to keep your data with your code, but the free storage limit is a single gigabyte.
If your data falls within that limit, you can use it to save the step of setting up a Zenodo record.
You won't be able to get a DOI in that case though.

### Further Reading
Rachael Tatman has a [great guide](https://www.kaggle.com/rtatman/reproducible-research-best-practices-jupytercon) going deeper into how to structure your code and data.
Her guide also contains a number of stylistic notes that might be useful and information on how to upload data to [Kaggle](kaggle.com), which is another place you can store your project.

## Library Management


## Random seeds
Many algorithms ubiquitous in biology use randomness so that they finish running prior to the heat death of the universe.
While such efficiency is a desirable property in a program, it also causes different results each time the program is run.
Thankfully random number generators on computers aren't truly random, they [draw from a list of pseudorandom numbers](https://en.wikipedia.org/wiki/Mersenne_Twister).
As a result, you can pick a location on that list to start picking numbers from.
This location is called a random seed.

How to set your random seeds depends on the program.
Often standalone programs will have an option to set their seed via a command line argument.
In Python, you can set your random seeds with the code below, though be aware that many libraries like numpy will require their own seeds to be set.

```python
import random

random.seed(42)
```

In R, you set random seed as follows:

```R
set.seed(42)
```

This might seem like an inconsequential thing, but it is nearly impossible to reproduce the results of your research if you don't know the random seed.
Pixar actually [ran into this problem](https://www.tested.com/art/movies/449542-finding-nemo-3d-interview/) when converting Finding Nemo into 3D,
so if you're waffling on whether to set all your random seeds, just do it for Nemo.


## Manual steps

<a href #readme></a>
## README

## Publish your code
