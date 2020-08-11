---
layout: post
title: How the Repo Gets Made
subtitle: A description of repo design
description: "A story of designing a repository"
cover-img: "/assets/img/lakeview.jpg"
citable: true
---

# Introduction

When I look at large projects' repositories, I find myself wondering how the authors decided how all the code fit together.
Likewise, when I was starting my transcriptional [model benchmarking project](https://github.com/greenelab/saged)
I was unable to find any articles on best practices for projects with lots of ML models and datasets.
This article is a response to both problems.

I will do my best to explain the iterations I went through that led me to my current project structure, the reasoning behind the structures, and the places I got ideas from.

## Project Background
To make sense of the design decisions, a little background information is necessary.
My current project (temporarily named SAGED) is to benchmark a number of classical and deep learning models to
determine whether semi-supervised learning and dimensionality reduction are helpful for improving model performance on gene expression data.
Many of the design decisions I make for the project are to ensure that the benchmark is as modular as possible to allow the addition of more models and datasets later on.

TODO change organization to be datasets, models, and benchmark logic

## Initial Plan
In the beginning I planned for the project to have a single file containing benchmarking logic and a `models.py` and `datasets.py` containing
python classes implementing models and classes respectively.
I had already had a planning meeting with other project stakeholders, so I had a good idea of [what functionality would be required](https://github.com/greenelab/saged/issues/3#issue-646243304).

## Defining Datasets

## Unifying Models
Very early on in implementing that design I realized that it would be hard to get models to behave uniformly.
I expected this ahead of time, and wrapped the pytorch models in a wrapper that implemented the same `fit` and `predict` functions as the scikit-learn models.
Unfortunately while the `fit` and `predict` methods could both take `dataset` objects, saving and loading models required different information for different classes of model.

## Defining Benchmarks

## Tying Everything Together

