---
layout: post
title: How to Convert Gene ID Formats in Python
description: "Changing ENSEMBL to gene symbol and back again"
cover-img: "/assets/img/traintracks.jpg"
tags: ['comp bio', 'ensembl', 'gene names']
---

## Intro 
Computational biologists who do machine learning research, have to live in the middle of two worlds.
They have to think about multiomics AND deep learning, understand [proper study splitting](https://autobencoder.com/2021-01-29-paper-evaluation) AND held out sets, and, most painfully, code in Python AND R.

Keeping track of two languages in a project often necessary because of library/StackoverFlow support being predominantly in one language depending on the subject.
However, if you can limit a project to a single language, then that's fewer dependencies and less installation time for future users.

This post is documents in Python one task that's already well-documented in R: converting [ENSEMBL ids](https://useast.ensembl.org/info/genome/stable_ids/index.html) to [gene symbols](http://www.informatics.jax.org/glossary/gene_symbol) and vice versa.

## The R Method
If you only care about the Python solution, go ahead and [skip forward one section](#python).

It may be helpful to understand 
There are a number of variations on [this theme](https://bioinformatics.stackexchange.com/a/5230).
The key things to look at are 
- What fields to return
- What subset of the data you'll pass in (filters/values)
```
library("biomaRt")
ensembl = useMart("ensembl",dataset="hsapiens_gene_ensembl")
getBM(attributes='hgnc_symbol', 
      filters = 'ensembl_gene_id', 
      values = ensemblsIDS, 
      mart = ensembl)
```

<a id="python"></a>
## The Python Method

### Download library

### Code 

### Explanation

## Conclusion
Writing a project in two languages is painful.
With luck, this information was the one piece that was missing, and now you can avoid one language entirely.
If not, hopefully it at least saved you some time.
