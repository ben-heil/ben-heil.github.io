---
layout: post
title: How to Use Object Oriented Programming to Save Time 
description: "The story of how I built a transcriptional machine learning benchmark starting from an empty repo"
cover-img: "/assets/img/lakeview.jpg"
citable: true
---

# Introduction

When I look at large projects' repositories, I find myself wondering how the authors decided how to fit all the code together.
I'll read the projects' associated papers, but their methods sections explain what is happening in the project, not why it is designed the way it is.

When I started my transcriptional [model benchmarking project](https://github.com/greenelab/saged), I was deliberate in searching for best practices in ML project design.
Unfortunately, I was unable to find any articles that explained the best way to structure code in a project with multiple machine learning models and datasets.
Over a few months of work and several missteps, I created my own way to structure such a project.

This post describes my failed attempts and the solution I decided on.
If you're interested in how [object oriented programming](https://docs.python.org/3/tutorial/classes.html) can be a useful tool,
or are looking for a way to organize a machine learning project, this post is for you.


## Project Background
To make sense of the design decisions, a little background information is necessary.
My current project (temporarily named SAGED) is to benchmark a number of classical and deep learning models to
determine whether semi-supervised learning and dimensionality reduction are helpful for improving model performance on gene expression data.
Because I've worked on ML projects before, I knew that the three main pieces I'd need to worry about were datasets, models, and analysis logic.
Many of the design decisions I make for the project are to ensure that the benchmark is as modular as possible to allow the addition of more models and datasets later on.

## Initial Plan
In the beginning I planned for the project to have a single file containing benchmarking logic and files `models.py` and `datasets.py` containing
python classes implementing models and classes respectively.
I had already had a planning meeting with other project stakeholders, so I had a good idea of [what functionality would be required](https://github.com/greenelab/saged/issues/3#issue-646243304).

## Defining Datasets
In order to get the datasets to work with all types of models, they have the same required functions as [pytorch datasets](https://pytorch.org/docs/stable/data.html#dataset-types).
They also need to be able to return all the data in the dataset to work with scikit-learn models.
Finally, since some of my data is labeled and some isn't, I knew I would need different classes to handle the two types.

My initial design for the datasets had a [base class](https://docs.python.org/3/glossary.html#term-abstract-base-class) called [ExpressionDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L13)
which defined the functions that all dataset classes were expected to have.
It then had [LabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L178) and [UnlabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L220)
classes outlining the functions that labeled and unlabeled datasets would need that the other wouldn't.
Finally, I put the actual logic into [RefineBioUnlabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L249)
and [RefineBioLabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L674), each of which inherited from their corresponding base classes.
To clarify the name, the expression compendium I'm working with comes from [refine.bio](refine.bio).

## Why So Many Base Classes?
If you're unfamiliar with object oriented programming, all the machinery may seem like overkill to you.
And to be fair, there are around 250 lines of code and comments in the [datasets file](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py) that
don't actually do any work.
There are two advantages of putting in the effort for all these base classes though.

First, I expect to add datasets other than the [refine.bio](refine.bio) compendium in the future.
By designing base classes with typed function signatures, I know exactly what the future dataset classes will need to do.
Python will even throw an error telling me which functions I didn't implement I forget something.

Second, having a set [API](https://francescolelli.info/programming/how-to-design-a-good-api-advanced-object-oriented-programming/) makes testing easier.
Since I know exactly what functions each class will implement and what their inputs and outputs should be, I can use the same test cases for multiple classes.

## Supervised Models
With the dataset objects completed, I moved on to implementing supervised models.
To find what the implementation issues were going to be, I began with a simple [logistic regression model](https://github.com/greenelab/saged/blob/dbd851c7d379842bdeaa2fcf321fae0962c6488a/saged/models.py#L181)
using scikit-learn, and a straightforward [pytorch neural net](https://github.com/greenelab/saged/blob/dbd851c7d379842bdeaa2fcf321fae0962c6488a/saged/models.py#L278).
In order to keep functions consistent between the models, I wrapped both models in classes
and had them inherit from an abstract class called [ExpressionModel](https://github.com/greenelab/saged/blob/dbd851c7d379842bdeaa2fcf321fae0962c6488a/saged/models.py#L107).
Unfortunately while the `fit` and `predict` methods for both classes could take `dataset` objects, initializing pytorch models required more parameters than sklearn ones.
I decided I would address the extra parameters with logic in the calling code, and moved on to create unsupervised models.

## Unsupervised Models
I created the PCA model in much the same way.
I started by creating an [UnsupervisedModel](https://github.com/ben-heil/saged/blob/3c7dcaf193069558207caee41976b26b47151fd2/saged/models.py#L511) abstract class, then implemented the PCA class itself.
I assumed that because the PCA class was another sklearn wrapper, it was pretty easy to get it to operate on UnlabeledDatasets from earlier.

## Writing The First Benchmark Script
Around this point in the implementation process I found myself struggling to nail down implementation details for the models and datasets.
Since I didn't know exactly how they were going to be used, it was hard to decide how they should interface with the outside world.
I decided to change tacks and start working on the analysis logic for the benchmarks.
In doing so, I realized that the way I was handling model creation wouldn't work since I wanted the models to be initialized with the same calling code.
The difference in parameters required in different models would make the calling code too complex if I continued down the current path so I needed a different system.

I remembered looking at a project that used [allennlp config files](https://docs.allennlp.org/v1.0.0rc3/tutorials/getting_started/walk_through_allennlp/configuration/), and decided to copy that style.
Instead of trying to force each model to have the same parameters passed in, I moved all the information necessary to create an instance of a model to [yml config files](https://github.com/ben-heil/saged/tree/0030174a8166758aa0696d0606579e84e495e2e8/model_configs).
For the sake of consistency I also wrote functions so the dataset objects could be initialized from config files.

The clear set of requirements in the benchmark script also made me realize that life would be much easier if I had more dataset utility functions and a dataset type that could hold both labeled and unlabeled data.
I [overhauled the datasets again](https://github.com/greenelab/saged/pull/10/files#diff-23f36de1d9afe152075c89a03c204d66) to make that dream a reality.

Actually writing a script to run an analysis gave a lot of focus to what the design should be for the rest of the project.
I wish I had done it sooner, or even started development by creating a script full of function stubs.

## Tying Everything Together
The config file system finally gave my project the modularity I was looking for.
The models and datasets were swappable without much effort, and they could be selected by individual command line arguments.
All that was left was to find a way [to run everything with one button press](https://ben-heil.github.io/2020-06-30-shoulddo/#button).

To automate running and paralellizing the code I decided to learn [Snakemake](https://snakemake.readthedocs.io/en/stable/).
I picked Snakemake over other workflow management systems simply because I know other people who use it who I could ask for guidance if I got stuck.
After a day of reading I managed to write an [initial Snakefile](https://github.com/greenelab/saged/blob/20180b37b43dbbf112ed7b874a2149a4cf0dba9a/Snakefile) that automatically ran all the benchmark scripts.

## More Features
The project is still [under development](https://github.com/greenelab/saged/commits/master), and I'm continuing to implement new features. 
Thankfully, with the hard design problems out of the way adding new classes and benchmark scripts is very easy.
For example, to implement a [pseudolabeling](http://deeplearning.net/wp-content/uploads/2013/03/pseudo_label_final.pdf) model I only had to write [two new functions](https://github.com/ben-heil/saged/blob/5d11a4d7c5fba2431c3e4ef07ae5549d0577f79f/saged/models.py#L867).
The rest of the moving parts were already taken care of in the PytorchSupervised class.

## Conclusion
The challenges you will have in building a repository won't be the same as mine.
I chose to optimize for a modular, easily automatable project, but those may not be your priorities.
That being said, the factors you need to consider will be the same even if their execution is different.
Thinking about your data, your model(s), your analysis, and how they all fit together is the crux of planning a machine learning project.
Hopefully having read the decisions I made will help you to make good decisions of your own on future projects.
