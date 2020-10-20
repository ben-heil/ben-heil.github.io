---                                                                                                                                                                                                         
layout: post                                                                                                                                                                                                
title: How to read Bio ML Papers
description: The things to look for in comp bio papers using machine learning
cover-img: "/assets/img/river_trail.jpg"                                                                                                                                                                    
---

# Intro
Designing machine learning (ML) experiments to solve biological problems is difficult because it requires expertise from multiple fields.
On one side, machine learning knowledge is required to understand the assumptions made by various model types.
On the other, biological knowledge lets the experimenter know which assumptions are actually warranted by biology and which are inappropriate.

A lot of cool science is done at the intersection of these two domains.
That being said, I frequently read papers that would be amazing, but the expertise is skewed towards one field or the other.

This article is designed to help people from both fields know what to look for in comp bio ML papers.


## Data Splitting (held out test set)
In machine learning it is necessary to have some measure of how well your model performs.
The standard way to do this is to [randomly split your data into three sets](https://stackoverflow.com/questions/38250710/how-to-split-data-into-3-sets-train-validation-and-test) known as the train, validation, and test set.
The performance of your model on each dataset tells you something different about your model.
The training set is the set of data your model is actually fit to, and good performance on the train set tells you that your model is actually training.

The validation set is a set of data not used in training the model.
If you are training multiple models, or a single model with different hyperparameters, the models' performance on the validation set tells you which model to actually use.

Finally, the test set is a set of data held out from the training process.
This data can either be pulled randomly from the original dataset, or it can be an external dataset.
The important part is that the dataset isn't used to make any decisions in training or model selection.
If that condition holds, then the test set performance is an estimate for how well a model should perform on new data.


## Data Splitting (Inter-study differences accounted for)
The previous section was probably obvious to people coming from a machine learning background.
Interestingly, determining the train/test/validation sets by randomly selecting samples from the dataset often fails in biology.
As stated previously, the seperation between the validation/testing sets and the training data is what allows them to be useful estimators of performance.
If you split data at the per-sample level, however, you'll end up leaking data between those sets.

For example, I work with [gene expression data](https://github.com/ben-heil/whistl/blob/master/notebook/data_exploration/compendium_eda.ipynb), where batch effects are very pronounced.
These batch effects are strong enough that the healthy and disease states from the same study look more similar to each other than healthy gene expression across studies (see images below).

<img src="/assets/img/post_img/inter_study_distance.png"/>
<img src="/assets/img/post_img/interdisease_distance.png"/>

If I were to split the data by randomizing samples into each group, the model would have a good idea of what the data in the test set looked like based only on the training data.
Then, when the model is applied to new studies it would take a huge performance hit because it wouldn't know anything about their data.
This type of issue is called [data leakage](https://www.kaggle.com/dansbecker/data-leakage).

## Data Leakage
- Preprocessing on all data (cite Jacob)

## Base Rate

## Correct metrics used

## Comparison to known biology

## Reasonable model assumptions

## Author Degrees of Freedom 
- Cite Arjun
