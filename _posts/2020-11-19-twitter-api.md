---
layout: post
title: Why I Don't Recommend Using the Twitter API
description: "The story of how I got my personal account banned for using the Twitter tweet downloading API to download tweets"
cover-img: "/assets/img/far_philly.jpg"
---

This past August I applied for and received a developer API key to download data from my timeline.
My goal was to train a sentiment analysis classifier (as in arxiv.org/abs/1912.00741), then run it on an aggregation of tweets in my timeline to see if/how the sentiment of tweets in general changed over time.
I downloaded a week's worth of data, but ultimately ended up shelving the project in early August as I learned the API doesn't allow for tweets older than 1 week to be downloaded.
Two months later, Twitter banned my account citing "multiple, repeat, and/or severe violations of Twitter's Developer Agreement and Policy."



It's funny, in early October I was thinking about wrapping up the blog and switching to Twitter-only shortform posts.
I knew that that meant signing away control over any writing I did, but I thought the odds of being kicked off were slim.
Now I'm very glad to have not lost all the posts I've made due to an overzealous spam detection algorithm.


##Other people with the same problem:
https://redfern.me/banned-from-twitter/
http://www.onurvarol.com/blog/twitter-suspension/
Not the same, but tweeted at twitter enough that they fixed it https://uxdesign.cc/twitters-algorithm-suspended-my-account-the-design-community-help-restore-it-d0976512b6f1

##Reasons to avoid twitter dev program:
If you fail your first application, you're locked out of the dev program forever with no appeal

Issues with Twitter API Policy should not be addressed to the Twitter API policy form

Twitter Support gets (literally) about a million tickets a week. 
The case number for my first appeal on October 13 was 0176458624 and the case number on my second on 

You increase your probability of getting banned, apparently.

##Disadvantages of Being Banned
Twitter keeps your information forever, refuses to delete your account, and doesn't let you request your own information
A "Welcome back" message every day

To use the API support form to ask about your suspension, you need your app ID and API key. 
To get your app ID and API key, you need to login to developer portal. 
To log into the developer portal you need to not be banned.

Twitter deprioritizes tweets that tag you

Twitter support emails back after roughly two weeks of waiting.
They do have a header that says they are "experiencing some delays", but that header has also been up [since June](https://web.archive.org/web/20200627013633/https://help.twitter.com/forms/general?subtopic=suspended).

