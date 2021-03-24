---
layout: post
title: How I Got Banned from Twitter and How You Can Help
description: "The story of how I got my personal account banned for using the Twitter tweet downloading API to download tweets"
cover-img: "/assets/img/far_philly.jpg"
---
**Update: my account got [unbanned on New Year's Eve](https://twitter.com/autobencoder/status/1344747555054350341).                                                                                         
I am happy to be back and appreciate everyone who Tweeted for me!** 

This past August I ([@autobencoder](https://twitter.com/autobencoder)) applied for and received a developer API key to download data from my Twitter timeline.
My goal was to train a sentiment analysis classifier, then run it on an aggregation of tweets in my timeline to see if/how the sentiment of tweets in general changed over time.
I downloaded some data, but ultimately ended up shelving the project in early August when I learned the API doesn't allow for tweets over one week old to be downloaded.
Two months later Twitter banned my account citing "multiple, repeat, and/or severe violations of Twitter's Developer Agreement and Policy."

Now, I've read the Developer Agreement, both before I applied for a dev account (because I'm a nerd) and after I got the ban.
I am confident that I didn't actually violate any policies, especially since [tons](https://data.world/crowdflower/apple-twitter-sentiment) 
[of](https://datahack.analyticsvidhya.com/contest/practice-problem-twitter-sentiment-analysis/)
[people](https://data.world/crowdflower/sentiment-self-driving-cars) 
[have](https://data.world/socialmediadata/twitter-us-airline-sentiment)
[created](https://data.world/drstevekramer/election-2016-twitter-analysis)
[similar](https://data.world/crowdflower/sentiment-self-driving-cars) 
[datasets](https://alt.qcri.org/semeval2017/task4/).
In fact, since I didn't release or plan to release the downloaded data, I am much less likely to have run afoul of the data publication and hydration parts of the agreement.
Ultimately I put in a developer application with a planned analysis, got the plan explicitly approved by Twitter, then got banned for it months later by what I assume was an automated process.

Normally none of this would be worth mentioning.
I would put in an account suspension appeal, it would be reviewed, and I would be unbanned.
Unfortunately, Twitter Support gets (literally) a million tickets a week.
The appeals process so far has consisted of me writing an impassioned plea, getting sent a form email two weeks later, and repeating the process.
I don't appear to be the only one with this problem, as a number of [academic accounts](https://twitter.com/onurvarol/status/1329347533244669952) have been [suspended with appeals denied](https://redfern.me/banned-from-twitter/) recently.

Based on my research, there seems to be only two options if I want to continue to use Twitter.
I can either create a new account, or try to raise the subject to Twitter's attention.
I don't think making a new account would be a sustainable option, as it's a clear violation of Twitter's [ban evasion policy](https://help.twitter.com/en/rules-and-policies/ban-evasion).
More likely it would leave me in the same situation I'm in now a few months down the line, except that I would have actually broken a rule at that point.

It looks like the only option I have left is to ask for your help.
If you know someone at Twitter, please ask them to get in touch with me or look at ticket number 0181283994.
If you don't know someone at Twitter, please spread this blog post around or tweet at Twitter Support.
Tweeting to raise the subject to Twitter's attention [has worked](https://uxdesign.cc/twitters-algorithm-suspended-my-account-the-design-community-help-restore-it-d0976512b6f1) [for others](https://twitter.com/onurvarol/status/1329347533244669952), so maybe it will work for me.

Thank you so much for your help, I hate asking people to tweet at accounts but I don't know of any other way.
I'm planning a project next year, and being able to communicate about it on Twitter is key. 

## Life on the Other Side
While I certainly don't recommend being banned from Twitter, I've had a unique opportunity to poke around at the Twitter suspended user experience for the past couple months.
Here are some fun glitches and catch-22s that arise when suspended:

- Twitter wants you to feel welcome, despite your suspension.
So much so that they either add a welcome back card to your timeline or ask for your friend's contact information every day.

<img src="/assets/img/post_img/welcome.jpg" width="25%">
<img src="/assets/img/post_img/friends.jpg" width="25%">

- This one is less fun and more intentional, but did you know that Twitter deprioritizes tweets that tag banned accounts?
When we were planning a [deep learning workshop](https://ben-heil.github.io/2020-10-08-workshop/), my co-organizer and PI found that their tweets discussing the workshop got much less engagement when they tagged me.

- When I first found out my account was suspended, I thought I might have been hacked.
I went to check my account access history, but it turns out that suspended accounts can't do that.

- The first appeal rejection email I got from Twitter was signed "Twitter API Policy."
"Oh, I guess I should send my appeal with the [API Policy Support Form](https://help.twitter.com/forms/platform)," I thought misguidedly.
Weeks later I was shown the error of my ways when I received a form email saying something like "we don't talk to people with suspended accounts".

- I may have skipped an intermediate step in the previous anecdote.
To submit the API policy support form I needed to have my API key and Twitter's ID for my app.
The most convenient way to retrieve those would be the developer portal, but you can't access it if you're suspended of course.

- If your account is suspended, you can't delete it.
While that's somewhat interesting on its own, you also can't use Twitter's data export tool on your account if it's suspended.
It gives the impression that banned accounts' data is supposed to stay on the servers forever as an example, but I imagine that's not really why they do it.
To be fair there is a form that you can fill out to request your data manually, though I haven't tried it.

## Conclusion
Thank you for listening to me ramble about being banned on Twitter.
If you're new to this blog, it's normally more science-focused and occasionally funny.
Stay tuned for a post on machine learning project design once I get my Twitter back.
