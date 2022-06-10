---
title: "Who should be the architect in an agile project?"
date: "2012-06-26"
categories: 
  - "agile"
  - "technology"
tags: 
  - "agile"
  - "architecture"
  - "software-architect"
---

I came across this question on Programmers.Stackexchange.com today:

> We are developing the agile way for a few months now and I have some troubles understanding the agile manifesto as interpreted by my colleagues. The project we are developing is a framework for future projects and will be reused many times in the next years.
> 
> Code is only written to fulfill the needs of the current user story. The product owner tells us what to do, but not how to do it. What would be right, in my opinion, because he is not implicitly a programmer.
> 
> The project advanced and in my eyes it messed up a little bit. After I recognized an assembly that was responsible for 3 concerns (IoC-Container, communication layer and project internal things), I tried to address this to my colleagues. They answered that this would be the result of applying YAGNI, because know one told them to respect that functionalities have to be split up in different assemblies for further use.
> 
> In my opinion no one has to tell us that we should respect the Separation of Concerns principle. On the other side, they mentioned to prefer YAGNI over SoC because it is less effort to implement and therefore faster and cheaper. We had changing requirements a lot at the beginning of the project and ended up in endless refactoring sessions, because to much has to be adapted.
> 
> Is it better to make such rather simple design decisions up front, even there is no need in the current situation, or do we have to change a lot in the later progress of the project?

Delay architectural decisions until as late as possible (but no later). You are going to know more about the problem in the future than you do right now, and thereby likely come up with smarter decisions.

For instance, imagine you are trying to decide up front whether to use MongoDB, some other NoSQL or traditional SQL for a project. Delay the decision: write a simple repository layer that will simply save the data in memory, or save data to a JSON file for now, and continue on. Eventually you'll have to replace this stub with something - but later on, you might know with more of a certainty which way to go: "hey, all of this really is a great fit for a document store" or "you know, SQL transactions would really help".

Its important to do this in conjunction with the concept of prioritizing the riskiest parts of your application to be developed first - that is, the parts you understand the least you should do before you do other work. This should mean you end up with an architecture that fits your solution, instead of forcing your solution later to fit in to your pre-selected architecture.

One way to look at it - in an Agile project, you're always doing architecture, instead of trying to do the architecture "up front". If a pair of programmers (you are pair programming right?) picks up a story and realizes that it requires an architectural discussion, they should have it. If they feel they should involve more devs, then they should do that. If this results in a desire to remove some technical debt caused by previous lack of architecture or previous poor architectural decisions, what we've done is had the devs write up a work item for this and work with the product owner to schedule it in. Generally, explaining it in terms of ROI: "we believe the removal of this technical debt will result in the following return", generally in terms of faster response times, or shorter future development cycles.

As far as the original question "who should be the architect in an agile project", I've always selected the most experienced programmer on the team that has a firm grasp on the code base, and given them the architect title. This should be a developer who is comfortable with leadership. Sometimes the architect is just helping the developers come up with a solution and guiding the process a little bit, sometimes they are making the call. In teams organized this way, the architect is contributing code to the project most of the time, and playing the "architect" role whenever required.

A [well-respected architect](http://lazyloading.blogspot.ca) I know recently made a [great post](http://lazyloading.blogspot.ca/2012/06/agile-architecture.html) that spells it out more effectively than I could.

### Update

Upon reflection, with regards to "selecting" an architect, I'm starting to think its an OK answer to the wrong question. Often, a self managing team elects their _own_ architect. You'll know who they are, because they're the person everyone asks for advice.

_This is a repost of an [answer](http://programmers.stackexchange.com/a/154161/1177) I provided at programmers.stackoverflow.com._
