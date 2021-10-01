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

There are a number of variations on [this theme](https://bioinformatics.stackexchange.com/a/5230) that show up if you google "convert ensembl to gene symbol", and the parameters are pretty well documented [here](https://www.rdocumentation.org/packages/biomaRt/versions/2.28.0/topics/getBM).
In short, you have `attributes`, which are the types of results you want (e.g. gene symbols), `filters`, which are the type of data you're passing in (e.g. ensembl gene ids), `values`, which is the data you want converted, and `mart` which is created in the previous step and tells biomaRt to look for the info.
``` R
library("biomaRt")
ensembl = useMart("ensembl",dataset="hsapiens_gene_ensembl")
ensembl_to_gensymbol <- getBM(attributes = 'hgnc_symbol', 
                              filters = 'ensembl_gene_id', 
                              values = ensemblsIDS, 
                              mart = ensembl)
```
<a id="python"></a>
## The Python Method

### Download library
We'll use the python [biomart package](https://pypi.org/project/biomart/) to make life eaiser.
To install it, just use `pip install biomart`.

### Code 
The python version of the code is more verbose (though to be fair most of the code just parses the output into a dict), so I'll walk through it in chunks.
If you just want the whole function for copy-pasting, [click here](https://gist.github.com/ben-heil/cffbebf8865795fe2efbbfec041da969).

-----

``` python
import biomart 


def get_ensembl_mappings():                                   
    # Set up connection to server                                               
    server = biomart.BiomartServer('http://uswest.ensembl.org/biomart')         
    mart = server.datasets['mmusculus_gene_ensembl']                            
```

This first block makes a connection to the server and tells the library which dataset to use. 
The first line doesn't need to be changed, but if you're working with organisms other than mice you may want to change your dataset.
A list of available datasets can be found in the "Selecting a BioMart database and dataset section of [this post](https://bioconductor.riken.jp/packages/3.4/bioc/vignettes/biomaRt/inst/doc/biomaRt.html).

-----

``` python
    # List the types of data we want                                            
    attributes = ['ensembl_transcript_id', 'mgi_symbol', 
                  'ensembl_gene_id', 'ensembl_peptide_id']
```

Explain attributes and link to list

-----

``` python
    # Get the mapping between the attributes                                    
    response = mart.search({'attributes': attributes})                          
    data = response.raw.data.decode('ascii')                                    
```
Give a brief description of requests package and why I'm indexing so many levels into the response

-----

``` python
    for line in data.splitlines():                                              
        line = line.split('\t')                                                 
        # The entries are in the same order as in the `attributes` variable
        transcript_id = line[0]                                                 
        gene_symbol = line[1]                                                   
        ensembl_gene = line[2]                                                  
        ensembl_peptide = line[3]                                               
                                                                                
        # Some of these keys may be an empty string. If you want, you can 
        # avoid having a '' key in your dict by ensuring the 
        # transcript/gene/peptide ids have a nonzero length before
        # adding them to the dict
        ensembl_to_genesymbol[transcript_id] = gene_symbol                      
        ensembl_to_genesymbol[ensembl_gene] = gene_symbol                       
        ensembl_to_genesymbol[ensembl_peptide] = gene_symbol                
```

Explain pasing code

-----

``` python
import biomart

def get_ensembl_mappings():                                   
    # Set up connection to server                                               
    server = biomart.BiomartServer('http://uswest.ensembl.org/biomart')         
    mart = server.datasets['mmusculus_gene_ensembl']                            
                                                                                
    # List the types of data we want                                            
    attributes = ['ensembl_transcript_id', 'mgi_symbol', 
                  'ensembl_gene_id', 'ensembl_peptide_id']
                                                                                
    # Get the mapping between the attributes                                    
    response = mart.search({'attributes': attributes})                          
    data = response.raw.data.decode('ascii')                                    
                                                                                
    ensembl_to_genesymbol = {}                                                  
    # Store the data in a dict                                                  
    for line in data.splitlines():                                              
        line = line.split('\t')                                                 
        # The entries are in the same order as in the `attributes` variable
        transcript_id = line[0]                                                 
        gene_symbol = line[1]                                                   
        ensembl_gene = line[2]                                                  
        ensembl_peptide = line[3]                                               
                                                                                
        # Some of these keys may be an empty string. If you want, you can 
        # avoid having a '' key in your dict by ensuring the 
        # transcript/gene/peptide ids have a nonzero length before
        # adding them to the dict
        ensembl_to_genesymbol[transcript_id] = gene_symbol                      
        ensembl_to_genesymbol[ensembl_gene] = gene_symbol                       
        ensembl_to_genesymbol[ensembl_peptide] = gene_symbol                
                                                                                
    return ensembl_to_genesymbol
```

Concluding remarks on the code (and maybe put the full function first?)

-----




## Conclusion
Writing a project in two languages is painful.
With luck, this information was the one piece that was missing, and now you can avoid one language entirely.
If not, hopefully it at least saved you some time.
