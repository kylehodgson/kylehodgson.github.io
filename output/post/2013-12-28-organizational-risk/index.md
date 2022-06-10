---
title: "Organizational Risk"
date: "2013-12-28"
categories: 
  - "agile"
tags: 
  - "agile"
  - "enterprise"
  - "evolutionary-architecture"
  - "organizational-risk"
---

Transforming an organization is a difficult business. One common pattern is the development team takes up the charge, practicing scrum and XP techniques. However, they may find their agile team working with a waterfall oriented Enterprise Architecture and Web Operations department. There’s an impedance mis-match here that can result in risk to your project, particularly if your definition of done includes “in use by real users” or even “installed in production”.

Your team is happily moving along, practicing evolutionary architecture, delaying architectural decisions until the last responsible moment, building stories in order of user value, and you’re finding that the other teams you rely on don’t appreciate that at all. Often these groups are an important part of your success; and they don’t like it when you come to them a few days or weeks before (or even after) a deploy happily announcing that your latest architectural evolution now requires network changes or more servers. They have their own schedules to deal with, their own budgets, their own projects — and they’re used to a process where, ideally, they deal with all of these decisions once.

Who’s right? If we try to give them our architectural plans up front, we’re knowingly giving up on the principle of delaying architectural decisions and the value that this can bring. If we solider on, pretending that these decisions don’t impact other groups, we’ll continue to encounter friction which will threaten our project’s success.

* * *

## Story Order Based on User Value

In their 2000 book, "[Planning Extreme Programming](http://www.amazon.com/gp/product/0201710919?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0201710919)", [Martin Fowler](http://www.martinfowler.com) and [Kent Beck](http://en.wikipedia.org/wiki/Kent_Beck) talk about how its important to let the product owner select the order that stories are played in. They suggest that the order should be based on value to the user. This is good advice — it’s difficult to predict the future, and by choosing to build software that’s useful to a user, you’re always creating value. The inverse of this is building infrastructure up front; spending your time writing frameworks and libraries might give you the impression of productivity, but in fact you haven’t created any value yet for your user.

We often temper this with the idea of pulling risky work forward, while we continue to balance this with respect for the principle about providing business value. If we know we’re going to have to build a [Flux Capacitor](http://backtothefuture.wikia.com/wiki/Flux_capacitor) for a particular story, and we haven’t quite figured out what one looks like or how to do it; it might make sense to pull that story forward —  the risk inherent in doing this work means we might be surprised about how long it takes. Also, if you build the Flux Capacitor last, and then realize that it needs foo-bar input, not baz-bar, we might get stuck refactoring lots of existing code.

But what about the kind of risk that comes from dealing with other groups in a large Enterprise? Playing a story that requires interaction with other groups might be considered risky.

Consider the following circumstances:

- The story requires the approval of the Enterprise Architecture group, as their approval is needed to use a library or framework that turns out to be the best way to implement the story
- The story requires certain changes to be made by the Web Operations group, for instance you’ve just realized that you’re going to make changes to the way your servers connect to one another.

The Architecture group might not like the library you’ve chosen. They might deny your request. The Operations group might not be able to make the specific changes you need to the network, or they may object to the changes outright. You might have intended to ship this story in the next few days, and now you find out you didn’t account for Organizational Risk.

Part of what makes this kind of risk difficult to manage is that it’s what is known as a ["Wicked Problem"](http://en.wikipedia.org/wiki/Wicked_problem), in that not any party knows all of the information. The development team doesn’t know what infrastructure constraints the Operations group is facing, and the Architecture group doesn’t know what challenges the development team is facing. The Product Owner knows that you have to work with these groups, but doesn’t exactly know how stories might impact them. Solving a Wicked Problem is a fundamentally social process.

* * *

## A Solution

One approach that can be effective is a scheduled meeting, once per iteration, with the kinds of groups that you need to interact with most. Bring a lead developer, a random developer, a member of the Enterprise Architecture group, someone from Operations. Try to avoid politics and stick to problems the team is trying to solve. Early and consistent feedback from the groups that you need to work with most closely can make a big difference. 

The old maxim says to delay decisions until the last _responsible_ moment. Make sure to consider the needs of others when you’re deciding what that last responsible moment is.
