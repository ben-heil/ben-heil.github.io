---
layout: post
title: How to Lie to Yourself with Data
description: ""
cover-img: "/assets/img/matchups.jpg"
tags: ['data science', 'data analysis']
---
## Intro
I work in a lab that does a lot of exploratory research.
We frequently use public data rather than generating our own, and "[fishing expeditions](#TODO casey fishing talk") are encouraged.

This type of research is important.
The large amounts of data collected by biologists give a window into the way that reality works, and sometimes patterns emerge in the data in aggregate that aren't noticed in the individual studies.
It's harder to see when you're looking at a small slice of reality rather than the entirety of the world's data.
That being said, working without a hypothesis can be dangerous. Peoples' brains are hardwired with bugs like confirmation bias that make it challenging to correctly interpret data.

## A Smashing Success?
Sometimes when I'm bored I'll use my data science skills to find answers to questions I'm curious about.
Around a year ago, I set out to answer one such question: who is the best character in Smash Bros Ultimate?

The pandemic had been in full swing for awhile, causing Smash Bros tournaments to move online.
This meant that in addition to recording match results in an online bracket, players also had to record the characters they picked for each match[^striking].
Now, for the first time ever, the character results for almost every competitive smash bros match were accessible through a [single public API](https://developer.smash.gg/docs/intro/).

Over a weekend I threw together the code to pull these results down, load them into a dataframe, and visualize interesting characteristics of the data.
The results were surprising to me.
I found the game to be balanced, as I expected, but several characters who were highly ranked were not frequently played.
However, after days with wrestling with the API[^API], I was ready to write up the project and be done with it.
I tidied up some visualizations, wrote my explanations, and [uploaded the post to reddit](#TODO).

The post was well received.
It was one of the top threads in the Smash Bros subreddit for a few days, got crossposted to other forums, and generated a lot of discussion on the results.
A few people even reached out to ask if they could use [my code and data](#TODO).
Everything was great until a Reddit user reached out and said "Is this like... totally wrong? I hope I'm missing something here. It doesn't look like you're setting the winner correctly."

It turns out they were correct. 
I quickly fixed the code, redid the analysis, wrote a retraction post, and contacted everyone who had asked for the code and data letting them know what happened.
While this experience was humbling, the point of this post isn't that I'm a worse programmer than I though.
The purpose is to point out mistakes I made that are avoidable and transferrable.

### Mistake 1: I assumed my code worked.
This is and unavoidable assumption, as everyone's code has bugs.
Yet, it's possible to mitigate the likelihood of damage from this assumption by using best practices for software engineering.
Some examples that apply for this project:

- I could have manually inspected the intermediate outputs of my code.
(Admittedly I did, but my mental model of what the results should look like was incorrect which led to the bug in the first place.

- I could have written unit tests to make sure each component of the code worked as expected.
Had I been forced to formalize my beliefs about the results, I probably could have noticed the issue with my mental model.

- I could have asked someone else to look at the code.
If this project had been something I was working on in Greenelab, it would have gone through code review.
One of my labmates may have caught the bug and saved me some heartache.

### Mistake 2: I assumed my data was representative of reality
My post analyzed >1M matches, and visualized an 80x80 matrix of matchup performances.
Despite all the trappings of quantitative research, what I was really doing was largely qualitative.
I said "this number is biggest and therefore best" instead of running an appropriate statistical test.
Had I tested the hypothesis "is this just a random matrix?" I would have seen that it was, and likely looked more carefully at my code.

### Mistake 3: I mistook hypotheses for evidence
Because I assumed my data was meaningful, I assumed the patterns in it were meaningful.
Just like I can see animals when I look at clouds, I can look at random data with the trappings of quantitative research and say "of course, this is exactly what I would expect."

Looking for patterns in data isn't a mistake in and of itself.
Pulling hypotheses out of the data is a useful method for hypothesis generation.
The trouble arose when I treated those hypotheses as evidence that the data was correct. 
I thought "Oh, this data says Bowser does well on small stages, which fits with my past beliefs that Bowser does well on small stages."
Reduced down, the logic behind this statement is "the data says X, and because the data says X the data must be more correct."

This logic isn't quite flawed; if I had [preregistered](#TODO) the hypothesis then it would be fine to see the data as evidence of that hypothesis.
The issue is that because of [confirmation bias](#TODO) I didn't weight findings that I would expect to be false as heavily.
When someone said "Fascinating that Lucina ranks up top, yet the darkest color in her row appears to be.... Marth," I didn't treat that as evidence against the data, I rationalized something to the effect of "Lucina and Marth are the same character, but Marth deals more damage with good spacing. Given that their [mirror match](#TODO) will be all about spacing, Marth would win," and treated that as further evidence for my data being valid.
Had I seen all the strange conclusions that the dataset implied and said "maybe this data is wrong" instead of "maybe I can explain this," I might have caught the bug before publication.

## Why this Matters
You're probably thinking, "Sure Ben, I get it, you have demonstrably failed at both research and programming. But why should I care?"

Learning how the world works is harder than you think, [even taking into account the fact that you know that](https://en.wikipedia.org/wiki/Hofstadter%27s_law).
I knew about all the mistakes I made going in, but I still managed to make them.
These mistakes show up time and time again in research, so it's imporant to be aware of them.
For example:

### Assuming code works
The assumption that research code is correct is obviously false. 
Even well-maintained software developed by strong programmers like BLAST [occasionally has major bugs](https://academic.oup.com/bioinformatics/article/35/9/1613/5106166).
The idea that there are no errors in all the one-off analysis code that computational biology is built on is obviously untrue.
It's possible to reduce the rate of bugs (and make them easier to discover) with [reproducible programming practices](/2020-06-16-mustdo/), but it's important to always be questioning your code's results.

### Assuming data represents reality
In the poster "[Neural correlates of interspecies perspective taking in the post-mortem Atlantic Salmon: An argument for multiple comparisons correction](http://prefrontal.org/files/posters/Bennett-Salmon-2009.jpg)," Bennett et al. put a dead fish through an fMRI machine.
They point out that if you fail to properly correct for multiple testing, you see a lot more brain activity than you'd expect in a dead fish.

While spurious correlations are pretty well known ([relavent XKCD](https://xkcd.com/882/)), the idea of data representing reality comes up a lot in models that learn from data.
If you assume that a dataset consisting entirely of individuals of European ancestry is representative of reality, you end up with [polygenic risk scores that fail to work on other populations](https://www.nature.com/articles/s41467-019-11112-0).

### Mistaking hypotheses for evidence
GANs / storytelling




### Footnotes
[^striking]: Some characters have favorable matchups against other characters. To resolve the circularity that can occur if players pick characters to counter each others' choices before a match, procedures are in place to [control character picking](https://www.reddit.com/r/smashbros/comments/1yt0ks/rules_for_character_picking_and_counter_picking/cfnhizf?utm_source=share&utm_medium=web2x&context=3). To make this possible in online tournaments, it is necessary to have a character selection feature built into the bracket.
[^API]: As with many APIs, entities stored in the database are accessed via ids. Unfortunately, the ids that I needed were entirely undocumented so I had to do things like examine the requests being made on tournament results pages to find which numbers mapped to which character icons.
