---
title: "When to use exceptions, vs. when to code defensively?"
date: "2012-03-11"
categories: 
  - "technology"
tags: 
  - "net"
  - "c"
  - "software-development"
---

_This blog post is a repost of an answer I wrote at [programmers.stackexchange.com](http://programmers.stackexchange.com/a/139175/1177)._

To answer the question of which is considered better practice, between exceptions and coding defensively:

In .NET, its common practice to avoid the overuse of Exceptions. One argument is performance: in .NET, throwing an exception is computationally expensive.

Another reason to avoid their over use is that is can be very difficult to read code that relies too much on them. Joel Spolsky's [blog entry](http://www.joelonsoftware.com/items/2003/10/13.html) does a good job of describing the issue.

At the heart of the argument is the following quote:

> The reasoning is that I consider exceptions to be no better than "goto's", considered harmful since the 1960s, in that they create an abrupt jump from one point of code to another. In fact they are significantly worse than goto's:
> 
> **1\. They are invisible in the source code**. Looking at a block of code, including functions which may or may not throw exceptions, there is no way to see which exceptions might be thrown and from where. This means that even careful code inspection doesn't reveal potential bugs.
> 
> **2\. They create too many possible exit points** for a function. To write correct code, you really have to think about every possible code path through your function. Every time you call a function that can raise an exception and don't catch it on the spot, you create opportunities for surprise bugs caused by functions that terminated abruptly, leaving data in an inconsistent state, or other code paths that you didn't think about.

Personally, I throw exceptions when my code can't do what it has been asked to do. I tend to use try/catch when I'm about to deal with something outside of my process boundary, for instance a SOAP call, a database call, file IO, or a system call. Otherwise, I attempt to code defensively. It's not a hard and fast rule, but it is a general practice.

Scott Hanselman also writes about exceptions in .NET [here](http://www.hanselman.com/blog/GoodExceptionManagementRulesOfThumb.aspx). In this article he describes several rules of thumb regarding exceptions. My favourite?

> You shouldn't throw exceptions for things that happen all the time. Then they'd be "ordinaries".
