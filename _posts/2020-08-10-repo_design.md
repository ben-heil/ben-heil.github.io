---
layout: post
title: How to Design and Structure an ML Project
description: "One way to build a transcriptional machine learning benchmark"
cover-img: "/assets/img/lakeview.jpg"
citable: true
---

# Introduction

When I look at large projects' repositories, I find myself wondering how the authors decided on the way their code would fit together.
I'll read the projects' associated papers, but their methods sections explain what is happening in the project, not why it is designed the way it is.

When I started my transcriptional [model benchmarking project](https://github.com/greenelab/saged), I was deliberate in searching for best practices in ML project design.
Unfortunately, I was unable to find any articles that explained the best way to structure code in a project with multiple machine learning models and datasets.
Over a few months of work and several missteps, I created my own way to structure such a project.

This post describes my failed attempts and the solution I decided on.
If you're interested in how [object oriented programming](https://docs.python.org/3/tutorial/classes.html) can be a useful tool,
or are looking for a way to organize a machine learning project, you might want to read this post.


### Project Background
To make sense of the design decisions, a little background information is necessary.
[My current project](https://github.com/greenelab/saged) is to benchmark a number of classical ML and deep learning models to
determine whether semi-supervised learning and dimensionality reduction are helpful for improving model performance on gene expression data.
Because I've worked on ML projects before, I knew going in that the three main pieces I'd need to worry about are datasets, models, and analysis logic.
Since I'll be continually addding more models and datasets as the project continues, many of the design decisions I've made ensure that the benchmark is as modular as possible. 

### Initial Plan
In the beginning I planned for the project to have three main files containing benchmarking logic, model implementation, and dataset objects.
I had already had a planning meeting with other project stakeholders, so I had a good idea of [what functionality would be required](https://github.com/greenelab/saged/issues/3#issue-646243304).

### Defining Datasets
In order to get the datasets to work with all types of models, I implemented the same required functions as [pytorch datasets](https://pytorch.org/docs/stable/data.html#dataset-types).
They also needed to be able to return all the data in the dataset to work with scikit-learn models.
Finally, since some of my data was labeled and some wasn't, I knew I would need different classes to handle the two types.

My initial design for the datasets had a [base class](https://docs.python.org/3/glossary.html#term-abstract-base-class) called [ExpressionDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L13)
which defined functions that all dataset classes were expected to have.
It then had [LabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L178) and [UnlabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L220)
classes outlining the functions specific to labeled and unlabeled data.
Finally, I put the logic specific to the [refine.bio expression compendium](https://www.refine.bio/compendia?c=normalized) into [RefineBioUnlabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L249)
and [RefineBioLabeledDataset](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py#L674), each of which inherited from their corresponding base classes.

### Aside: Why So Many Base Classes?
If you're unfamiliar with object oriented programming, all the machinery may seem like overkill to you.
And to be fair, there are around 250 lines of code and comments in the [datasets file](https://github.com/greenelab/saged/blob/a8a89d36873c79fa1cdd6ad8ee893d18f3633747/saged/datasets.py) 
just describing what other classes will do.
There are two advantages of putting in the effort for all these base classes though.

First, I expect to add datasets other than the [refine.bio](https://www.refine.bio/) compendium in the future.
By designing base classes with typed function signatures, I know exactly what the future dataset classes will need to do.
Python will even throw an error telling me which functions I didn't implement if I forget something.

Second, having a set [API](https://francescolelli.info/programming/how-to-design-a-good-api-advanced-object-oriented-programming/) makes testing easier.
Since I know exactly what functions each class will implement and what their inputs and outputs should be, I can use the same test cases for multiple datasets.

### Supervised Models
With the dataset objects completed, I began implementing supervised models.
To find what the implementation issues were going to be, I began with a simple [logistic regression model](https://github.com/greenelab/saged/blob/dbd851c7d379842bdeaa2fcf321fae0962c6488a/saged/models.py#L181)
using scikit-learn, and a straightforward [pytorch neural net](https://github.com/greenelab/saged/blob/dbd851c7d379842bdeaa2fcf321fae0962c6488a/saged/models.py#L278).
In order to keep functions consistent between the models, I wrapped both models in classes
and had them inherit from an abstract class called [ExpressionModel](https://github.com/greenelab/saged/blob/dbd851c7d379842bdeaa2fcf321fae0962c6488a/saged/models.py#L107).
Unfortunately while the `fit` and `predict` methods for both classes could take `dataset` objects, initializing pytorch models required more parameters than sklearn ones.
I decided I would address the extra parameters with logic in the calling code, and moved on to create unsupervised models.

### Unsupervised Models
I created the PCA model in much the same way.
I wrote an [UnsupervisedModel](https://github.com/ben-heil/saged/blob/3c7dcaf193069558207caee41976b26b47151fd2/saged/models.py#L511) abstract class, then implemented the PCA class itself.
Because the PCA class was another sklearn wrapper, it was pretty easy to get it to operate on UnlabeledDataset objects from earlier.

### Writing The First Benchmark Script
Around this point in the implementation process I found myself struggling to nail down implementation details for the models and datasets.
Since I didn't know exactly how they were going to be used, it was hard to determine how they should interact with the outside world.

I decided to change tacks and start working on the analysis logic for the benchmarks.
In doing so I realized that the way I was handling model creation wouldn't work. 
Since I wanted the models to be initialized with the same calling code, the difference in parameters between models would make the calling code too complex.
If I didn't want to rewrite all the benchmarks each time I added a model I'd need a different system.

I remembered looking at a project that used [allennlp config files](https://docs.allennlp.org/v1.0.0rc3/tutorials/getting_started/walk_through_allennlp/configuration/), and decided to copy that style.
Instead of trying to force each model to have the same parameters passed in, I moved each model's parameters to [yml config files](https://github.com/ben-heil/saged/tree/0030174a8166758aa0696d0606579e84e495e2e8/model_configs).

### Dataset Overhaul
Writing the benchmark script made me realize that unsupervised learning algorithms would need to operate on both labeled and unlabeled data.
There wasn't a clean way to do that with the existing dataset objects, so I wrote [a new one.](https://github.com/greenelab/saged/blob/fd0c752a347441646de243b3280280266e4edc07/saged/datasets.py#L303)
I also went ahead and modified the dataset classes to use config files to match the models.

Actually writing a script to run an analysis gave a lot of focus to what the design should be for the rest of the project.
I wish I had done it sooner, or even started development by creating a script full of function stubs.

### Tying Everything Together
The config file system finally gave my project the modularity I was looking for.
The models and datasets were swappable without much effort, and they could be selected by individual command line arguments.
All that was left was to find a way [to run everything with one button press](https://ben-heil.github.io/2020-06-30-shoulddo/#button).

To automate running and paralellizing the code I decided to learn [Snakemake](https://snakemake.readthedocs.io/en/stable/).
I picked Snakemake over other workflow management systems simply because I knew other people using it who I could ask for guidance.
After a day of reading I managed to write an [initial Snakefile](https://github.com/greenelab/saged/blob/20180b37b43dbbf112ed7b874a2149a4cf0dba9a/Snakefile) that automatically ran all the benchmark scripts.

### More Features
The project is still [under development](https://github.com/greenelab/saged/commits/master), and I'm continuing to implement new features. 
Thankfully, with the hard design problems out of the way adding new classes and benchmark scripts is very easy.
For example, to implement a [pseudolabeling](http://deeplearning.net/wp-content/uploads/2013/03/pseudo_label_final.pdf) model I only had to write [two new functions](https://github.com/ben-heil/saged/blob/5d11a4d7c5fba2431c3e4ef07ae5549d0577f79f/saged/models.py#L867).
The rest of the moving parts were already taken care of in the PytorchSupervised class.

### Conclusion
The challenges you will have in building a repository won't be the same as mine.
I chose to optimize for a modular, easily automatable project, but those may not be your priorities.
That being said, the factors you need to consider will be the same even if their execution is different.
Thinking about your data, your model(s), your analysis, and how they all fit together is the crux of planning a machine learning project.
Hopefully having read the decisions I made will help you to make good decisions of your own.
