---
layout: post
title: Reproducible Programming for Biologists Who Code
subtitle: Part 1: Must Dos
date: 2020-06-17
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

#### Further Reading
Atlassian has good tutorials for [how to set up and use git as a version control system.](https://www.atlassian.com/git/tutorials/setting-up-a-repository)

Hosting your code in the cloud leads to easier backups and sharing.
[GitHub](https://github.com), [GitLab](https://about.gitlab.com/), and [BitBucket](https://bitbucket.org/product) are all good ways to do this.
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

#### Further Reading
Rachael Tatman has a [great guide](https://www.kaggle.com/rtatman/reproducible-research-best-practices-jupytercon) going deeper into how to structure your code and data.
Her guide also contains a number of stylistic notes that might be useful and information on how to upload data to [Kaggle](https://www.kaggle.com), which is another place you can store your project.

<a href #packages></a>
## Library Management
Installing libraries (or packages or modules, depending on your language of choice) is a great way to add functionality to your code without having to implement it yourself.
Most analyses will use libraries not installed by default.
Consequently, it is necessary to specify the location and versions of these packages.
The best way to do so is language dependant, and I'll cover Python and R since they're the most commonly used languages in biology.

### Python
There are two main schools of thought in Python package management.
One uses [venv](https://docs.python.org/3/library/venv.html) and [pip](https://pypi.org/project/pip/) to handle dependencies.
Under this system packages are installed using pip and which packages are used is determined by the current venv virtual environment.
There are [good](https://www.dabapps.com/blog/introduction-to-pip-and-virtualenv-python/) [tutorials](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)
on how to use venv and pip, so I won't go over that here.
The key part for reproducibility is the requirements.txt file.
The [requirements.txt](https://note.nkmk.me/en/python-pip-install-requirements/) file lists which versions of which packages you are using, allowing others to install the same libraries with a single `pip install -r requirements.txt` command.

The second school combines virtual environments and package installation together into a single tool called [Anaconda](https://www.anaconda.com/products/individual).
In addition to combining two tools together, Anaconda also has the ability to manage your R and Python packages simultaneously (sometimes).
Again, there are many tutorials on exactly [how to use Anaconda](https://docs.anaconda.com/anaconda/navigator/tutorials/) so I won't get into it.
The part to pay attention to for the sake of reproducibility is the environment.yml file, which plays the same role that the requirements.txt file did in pip.

Regardless of which system you choose, the important part is that you keep the file containing your package information up to date.
Ideally, before you publish your code you should reinstall all your packages onto another computer or virtual environment and make sure everything still runs.

### R
I am by no means an expert on R.
That being said, I will frequently find myself needing to use libraries from R in a largely python-based analysis.
While Anaconda works well for me, it can be difficult to find the correct channels for R libraries.
If your project is entirely R-based, a more R-oriented package manager might be better.

As far as I can tell, [Packrat](https://rstudio.github.io/packrat/) seems to be the best one.
It keeps track of which libraries you have installed in a given directory, and allows you to share the library information and your code with the `packrat::bundle()` command.
Details on its usage can be found [on its website](https://rstudio.github.io/packrat/walkthrough.html).

#### Further Reading
- If you don't want to use the command line, [Anaconda Navigator](https://docs.anaconda.com/anaconda/navigator/) is a graphical user interface that lets you do the same things as standard Anaconda.
- If you're an RStudio user, [this tutorial](https://rstudio.github.io/packrat/rstudio.html) on Packrat will probably be more useful to you than the one linked above.


## Random seeds
Many algorithms ubiquitous in biology use randomness so that they finish running prior to the heat death of the universe.
While such efficiency is a desirable property in a program, it also causes different results each time the program is run.
Thankfully random number generators on computers aren't truly random, they [draw from a list of pseudorandom numbers](https://en.wikipedia.org/wiki/Mersenne_Twister).
As a result, you can pick a location on that list to start picking numbers from.
This location is called a random seed.

How to set your random seeds depends on the program.
Often standalone programs will have an option to set their seed via a command line argument.
In Python, you can set your random seeds with the code below, though be aware that many libraries like NumPy will require their own seeds to be set.

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
A few weeks ago someone opened an issue on one of my GitHub repositories.
Their comment said something to the effect of "Your code opens a file with this function, but that shouldn't work if the file is actually in the format you stated."
Upon examining the code I realized that they were correct, my code shouldn't work.
I then had to think back to design decisions I had made four years prior to figure out why I did what I did.
It turns out that I had written a one-liner to convert the format of all my data files, but never bothered to record it as a script.

Don't be like Ben circa 2016.
Even if something is easy enough to do that it only takes a single command line command, it should probably be made into a bash script.
Doing so makes it possible for others to reproduce your analysis, and saves you from having to remember things that you did years in the past.


<a href #readme></a>
## README
In a lot of ways your code is a crystallization of your thought process.
While this makes coding beautiful as an art form, it also means that how to use your code may not be immediately apparent to others.
The standard way to help bridge the gap between your thoughts and others' is to provide an instruction manual called a README.
A barebones README should contain three things:

1. A description of what the project does
2. Instructions on how to install the dependencies for your code (which will be easier if you [use some type of package manager](#packages)
3. Instructions on how to reproduce your analysis (which scripts to run, how, and in which order)

There are more sections that can be added to a README, which will be covered in future posts.
For now though, the three components listed above are critical for others to be able to run your code.

## Publish your code
"Code available upon request" is equivalent to saying "Methods section available upon request".
Unless every detail of your implementation is written into your methods section, without your code it isn't possible to reproduce your analysis.
Thankfully, platforms like [GitHub](https://www.github.com) and [BitBucket](https://www.bitbucket.com) make it free and easy to publish your code.

#### Further Reading
- [A guide to uploading code to GitHub](https://help.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line)
- [A guide to uploading code to BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/push-code-to-bitbucket-861185309.html)
