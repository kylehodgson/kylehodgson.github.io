---
title: "A love song for C#"
date: "2010-01-14"
categories: 
  - "technology"
---

I found the following conversation online today.  For non-technical readers, it boils down to something like this:

c# guy: "Hey, here's a simple way to do something that is sometimes a gotcha."

Pierre, confused c++ reader: "That can never work! While the code you wrote looks nice and simple, it actually has lots of hidden evils that are sure to ruin any computer it runs on!"

another c# guy: "We weren't talking about C++, Pierre. We were talking about C#."

So what's it all about? C# and C++ are both programming languages, Microsoft wrote one of them, supports both of them.  Both are powerful, you could argue that C++ is a bit more powerful - but in the kind of way where its often possible that things that look easy are really, really wrong.  c# is much nicer in this way; it's very very easy to write C++ in a way that's wrong.

The [conversation](http://www.calcaria.net/c-sharp/2006/03/convert-char-to-string.html):

Paul:

> char\[\] is quite convenient if you're reading file data using StreamReader, but it doesn't offer the same functionality as string does. Here's one way I've found of converting:
> 
> char\[\] charBuf = new char\[1024\]; // Example char array
> 
> string s = ""; // Example empty string
> 
> s = new string(charBuf); // Create new string passing charBuf into the constructor

Pierre said... No!

> _new string(charBuf)_ returns a _string\*_ but _s_ is not a _string\*_! And if it was, who will free it's memory?
> 
> To convert a _char\*_ to a _string_ you may use :
> 
> _s.insert(0, charBuf);_
> 
> And if you are not sure that _s_ is empty, use _s.clear()_ before.

findepi said

> Pierre, it's C# not C++ :)

As soon as I read that line "It's C#, not C++", I sighed in relief. I hate to be pro Microsoft, but it really is better.
