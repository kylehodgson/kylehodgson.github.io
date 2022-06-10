---
title: "Should I start with the OO model or the ER model?"
date: "2012-03-13"
categories: 
  - "agile"
  - "technology"
tags: 
  - "er-design"
  - "oo-design"
---

_This blog entry is a repost of an answer I wrote at [programmers.stackexchange.com](http://programmers.stackexchange.com/questions/139545/should-we-do-er-modeling-or-oo-modeling-first)._

If I am building an application from scratch, should I start with the OO model or the ER model?

You might want to try to observe the principle of delaying architectural decisions as long as possible. The thought goes, that you will know more in the future about your problem domain than you do right now - so, any decisions you make today are suspect.

Another good principle to pair this with might be to try to attempt the riskiest parts of your requirements first - the thought being that if you do the easy parts, then find that the risky parts move you in a different direction, you don't have to re-do the easy parts. Risky here meaning things you aren't sure how you should do them.

Given these two, and given that I often try to approach things from an OO perspective, you might try to start with an OO model of the riskiest parts of your application first, and implement a least possible amount of code that can work that satisfies the risky requirements. Then, begin expanding your OO model as needed to add functionality you are going to need. All the while, you can completely delay your decision on whether to use SQL or NoSQL or flatfiles or cloud storage or whatever ... and you may eventually find you don't want relational at all (obviating the need for an ER model).
