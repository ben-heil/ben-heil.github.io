---
layout: post
title: Science vs Research
description: "The institution, the action, the difference"
cover-img: "/assets/img/traintracks.jpg"
tags: ['science', 'research', 'academia']
---

"The only difference between screwing around and science is writing it down"  
-Alex Jason by way of Adam Savage

## Intro
TODO intro paragraph:
Open source code is hard to make a business out of?
DL companies have started selling instead of open sourcing?


A common objection to open sourcing academic code is that it's difficult to make a business out of open source code.

Science - The process of transmitting information about the world into the future.
Research - The process of uncovering something about how the world works.

You can do science without doing research.
Websites like [Zenodo] that archive scientific data are doing science.
Academic journals, for all their faults, are doing science.
Medieval Irish monks [preserving books](https://en.wikipedia.org/wiki/How_the_Irish_Saved_Civilization) across the fall of empires?
Also science.

Research, on the other hand, doesn't require communication.
A person who discovers a new truth about the universe and keeps it secret did research, but they didn't do science.
Likewise, a company [discovering](https://www.nature.com/articles/s41586-019-1799-6) how [to solve](https://openai.com/blog/better-language-models/) a [problem](https://arxiv.org/pdf/2005.14165.pdf) that decides to keep the solution a secret so they can [sell it](https://openai.com/blog/openai-api/) is still doing research.

And, of course, research and science aren't mutually exclusive.
Research is frequently done in an academic setting with the goal of better understanding how the world works and passing on that knowledge.
After all, for the institution of science to have information to transmit, someone has to do research to learn that information.

## Research In Industry

Companies are very good at doing research in fields that benefit them.
Huge advances in the fields of computer science and information theory came out of [Bell Labs](https://en.wikipedia.org/wiki/Bell_Labs#Nobel_Prizes_and_Turing_Awards) and [Xerox PARC](https://www.fastcompany.com/1663451/how-i-helped-invent-the-touchscreen-in-1979-at-xerox-parc).
Likewise, the frameworks underpinning modern deep learning — [Transformers](https://github.com/huggingface/transformers), [Pytorch](pytorch.org/), and [TensorFlow](https://www.tensorflow.org/) — were all developed by companies.
However, corporations are designed to optimize profit.
When blue sky research becomes a promising new product, even companies dedicated enough to open source/scientific ideals to [put it in their name](https://openai.com/) end up holding on to their models and source code instead of publishing them as scientific works.

This transition isn't a discrete change, but rather a shift in priorities over time.
As a result, a tension starts to form between business interests and employee interests.
The scientists who work for the company want their work to be as widely available as possible, because "I wrote the software you use every day" is much easier to fit on a resume than "I wrote the software that some businesses use to build products you may have heard of."[^dev]
The company, on the other hand wants to make money so that it can do things like pay the scientists that work for it.

This tension leads to compromises that are bad science but less damaging to the business.
Such papers/blog posts/presentations typically take the form of "We did a cool thing, here are some of the details, no you can't have the code or models unless you buy them from us."
When pushback arrives, the company responds with something like "[...its release is therefore not feasible. However, all experiments and implementation details are described in sufficient detail in the Supplementary Methods section to support replication](https://www.nature.com/articles/s41586-019-1799-6)"

I don't believe publishing a paper without models or code is [good science](https://www.nature.com/articles/s41592-021-01256-7).
But regardless of the more subjective questions of whether it's good science, claims that you can reproduce a model from a methods section just aren't true.

As a case study, let's look at [AlphaFold2](https://deepmind.com/blog/article/alphafold-a-solution-to-a-50-year-old-grand-challenge-in-biology).
The DeepMind scientists gave a [conference presentation](https://predictioncenter.org/casp14/doc/presentations/2020_12_01_TS_predictor_AlphaFold2.pdf) and wrote a blog post, but didn't release any source code or models.

Because of the stunning results, one of the [top labs in the protein folding field](https://www.bakerlab.org/) and an [open source group](https://www.eleuther.ai/) known for replicating large DL architectures set out to create an open source version of AlphaFold2.
Baker Lab [finished first](https://pubmed.ncbi.nlm.nih.gov/34282049/), but despite their expertise ended up with a model that performed less well than AlphaFold2 had.
To add insult to injury, once an open-source version of model was published, DeepMind announced that they would publish their own paper and open source AlphaFold2.

To be clear, I don't think that industry research is a bad thing.
AlphaFold2 is a huge advance in protein structure prediction and is an exciting advancement in the field.
What I disagree with are not-quite-science publications that do a great job of advertising a company but a bad job of transmitting knowledge to the world.

## So What
This trend of refusing to open source models is accelerating.
However, academic publications aren't really the right venue for descriptions of these models.
Because they are primarily advertising material, journals should not be publishing them, and maybe preprint servers shouldn't either.

### Footnotes
[^dev]: Really, being an industry scientist in a company that lets you publish your work is the best of both worlds. You get all the name recognition of being a freelance open source developer with all the money and institutional support that comes from not being a freelance open source developer.
