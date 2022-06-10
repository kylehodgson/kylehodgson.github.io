---
title: "How to get past the \"But I can't test my legacy code\" objection."
date: "2012-03-18"
categories: 
  - "technology"
---

Maybe you're resistant to unit testing because you believe its not worth the effort to wrap your legacy code in tests. "It was all written before this test driven development nonsense was popular - it just wasn't designed to be tested". Perhaps this is compounded by the fact that most of your new features are being added to a legacy code base. "Test driven is for green field projects" you might think, and keep going about your business. I've known people who thought that way for a long time - even people who longed to find a way, but were disheartened when it just seemed too difficult.

If you have these kinds of issues, I strongly recommend refactoring the existing source code (as opposed to a rewrite) using the patterns found in the book "[Working Effectively With Legacy Code](http://www.amazon.ca/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)".

The book details several mechanisms for efficiently covering legacy code in unit tests, so that you can then begin to safely refactor the code. The book is broken in parts, one describing the philosophy behind the approach, and then several chapters that solve particular problems, such as "It takes forever to make a change", "I don't have much time and need to change it", and "I can't get this class in to a test harness". Each of these chapters has detailed, proven techniques that help you learn how to apply best practices in testing to real world problems.

Reading the book left me with a very real sense that "we are not alone" ... many of us, or perhaps all of us, are working with complex code bases that have become difficult to manage. The techniques listed in the book have given many people saddled with legacy code bases hope, and every developer who has read it has been able to apply the concepts _immediately_.

Joel Spolsky's [blog post](http://www.joelonsoftware.com/articles/fog0000000069.html) does a great job of explaining why its best to keep an existing, working code base as opposed to starting from scratch. I've chosen a quote from the article that sums it up, but its a fantastic read.

> "There's a subtle reason that programmers always want to throw away the code and start over. The reason is that they think the old code is a mess. And here is the interesting observation: they are probably wrong. The reason that they think the old code is a mess is because of a cardinal, fundamental law of programming:
> 
> It’s harder to read code than to write it.".
> 
> \-[http://www.joelonsoftware.com/articles/fog0000000069.html](http://www.joelonsoftware.com/articles/fog0000000069.html)
