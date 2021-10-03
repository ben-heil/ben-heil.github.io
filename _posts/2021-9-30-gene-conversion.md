---
layout: post
title: How to Convert Gene ID Formats in Python
description: "Changing ENSEMBL to gene symbol and back again"
cover-img: "/assets/img/traintracks.jpg"
tags: ['comp bio', 'ensembl', 'gene names']
---

## Intro 
Computational biologists often have to code in both Python and R.

Using both languages in a single project is often necessary. 
Either components of the project will rely on libraries in different languages, or all the Stack Overflow help for the components will be in R or Python only.
However, if you can limit a project to a single language, then you'll have fewer dependencies and less installation time for future users.

This post documents in Python one task that's already well-documented in R: converting [ENSEMBL ids](https://useast.ensembl.org/info/genome/stable_ids/index.html) to [gene symbols](http://www.informatics.jax.org/glossary/gene_symbol) and vice versa.


## The R Method
If you only care about the Python solution, go ahead and [skip forward one section](#python).

``` R
library("biomaRt")
ensembl = useMart("ensembl",dataset="hsapiens_gene_ensembl")
ensembl_to_gensymbol <- getBM(attributes = 'hgnc_symbol', 
                              filters = 'ensembl_gene_id', 
                              values = ensemblsIDS, 
                              mart = ensembl)
```

There are a number of variations on [this theme](https://bioinformatics.stackexchange.com/a/5230) that show up if you google "convert ensembl to gene symbol", and the parameters are pretty well documented [here](https://www.rdocumentation.org/packages/biomaRt/versions/2.28.0/topics/getBM).
In short, you have `attributes`, which are the types of results you want (e.g. gene symbols), `filters`, which are the type of data you're passing in (e.g. ensembl gene ids), `values`, which are the data you want converted, and `mart` which is created in the previous step and tells biomaRt where to look for the info.
<a id="python"></a>
## The Python Method

We'll use the python [biomart package](https://pypi.org/project/biomart/) to make interacting with BioMart servers easier[^biomart].
To install it, use `pip install biomart`.

The Python version of the code is more verbose (though to be fair most of the code is for parsing the output into a dict), so I'll walk through it in chunks.
If you want the whole function for copy-pasting, [click here](https://gist.github.com/ben-heil/cffbebf8865795fe2efbbfec041da969) or scroll to the end of the section.

-----

``` python
import biomart 


def get_ensembl_mappings():                                   
    # Set up connection to server                                               
    server = biomart.BiomartServer('http://uswest.ensembl.org/biomart')         
    mart = server.datasets['mmusculus_gene_ensembl']                            
```

This first block makes a connection to the server and tells the library which dataset to use. 
The first line won't typically need to be changed, but if you're working with organisms other than mice you may want to change your dataset.
A list of available datasets can be found in the "Selecting a BioMart database and dataset" section of [this post](https://bioconductor.riken.jp/packages/3.4/bioc/vignettes/biomaRt/inst/doc/biomaRt.html).

-----

``` python
    # List the types of data we want                                            
    attributes = ['ensembl_transcript_id', 'mgi_symbol', 
                  'ensembl_gene_id', 'ensembl_peptide_id']
```

This list contains the different ids that we want to map to each other.
The names of these attributes are dependent on the database, so you'll want to change them if you aren't mapping mouse gene ids to each other.
If you're unsure of which attributes are present in your dataset, you can call `mart.show_attributes()` which will print them out.

-----

``` python
    # Get the mapping between the attributes                                    
    response = mart.search({'attributes': attributes})                          
```

This line sends your query to the database, telling it to retrieve the attributes you requested.
The example code here is a little different than the R version since it doesn't have a `filters` or `values` field.
If you want to only return results for your favorite ENSEMBL gene ids, you could use something like the line below for your search parameters:

``` python
# ALTERNATE SEARCH PARAMETERS
{'attributes': attributes, 
 'filters': {'ensembl_gene_id': <A LIST OF YOUR IDS>}}
```
Because of the dictionary structure of the search parameters, you don' have to use `value = ` like in R.
Instead, pass the gene ids as a list of values with your attribute as the key.

---

``` python
    # If someone puts an emoji in a gene name, this line will break 
    data = response.raw.data.decode('ascii')                                    
```
If you're familiar with the [requests](https://docs.python-requests.org/en/latest/) library, this statement might look familiar to you.
It takes the request results and converts them from a binary string to an easier to work with text string.

-----

``` python
    ensembl_to_genesymbol = {}                                                  
    for line in data.splitlines():                                              
        line = line.split('\t')                                                 
        # The entries are in the same order as in the `attributes` variable
        transcript_id = line[0]                                                 
        gene_symbol = line[1]                                                   
        ensembl_gene = line[2]                                                  
        ensembl_peptide = line[3]                                               
                                                                                
        # Some of these keys may be an empty string. If you want, you can 
        # avoid having a '' key in your dict by ensuring the attributes
        # have a nonzero length before adding them to the dict
        ensembl_to_genesymbol[transcript_id] = gene_symbol                      
        ensembl_to_genesymbol[ensembl_gene] = gene_symbol                       
        ensembl_to_genesymbol[ensembl_peptide] = gene_symbol                
```
This part is all base python.
Each line in the response from BioMart is a tab separated list containing the attributes you requested in the order you requested them.
If that attribute doesn't have an entry (e.g. the gene doesn't code for a peptide), then the entry will empty instead.

Knowing this, you can split the line, assign the values you want to variables, and add each entry to the mapping dict.

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
Once you put all the pieces together, you get the function above.

-----


## Conclusion
Writing a project in two languages is painful.
With luck, this information was the one piece that was missing, and now you can avoid one language entirely.
If not, hopefully it at least saved you some time.

### Footnotes
[^biomart]: biomart is a Python package that interacts with BioMart web servers, not to be be mistaken with biomaRt, which is an R package for the same thing. I tried to get the capitalization correct to minimize confusion, but [capitalization is hard](https://twitter.com/SwiftOnSecurity/status/569989992233213952).
