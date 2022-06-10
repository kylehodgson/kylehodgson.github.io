---
title: "How can I host applications in IIS that aren't ASP.NET web applications?"
date: "2012-03-28"
categories: 
  - "technology"
---

By leveraging the [Windows Communications Foundation](http://msdn.microsoft.com/en-us/netframework/aa663324) framework, you can create services over a number of different network protocols. This includes HTTP, HTTPS, MSMQ, and even TCP/IP sockets. In addition to these, it also support Named Pipes for connections between two processes on the same machine.

IIS can host applications developed for WCF, even if they aren't HTTP or HTTPS based.

For more in-depth information about the different protocols that WCF supports, and information about the relative strengths and weaknesses of each, [MSDN](http://msdn.microsoft.com/en-us/library/ms733769.aspx) has good information about this.

While many people have argued that WCF is complicated to configure, it does an excellent job of allowing you to focus on writing code at a fairly high level of abstraction without worrying about the actual transport layer implementation details. I know of a number of projects that have made excellent use of the MSMQ transport in particular in order to enable durable transports that survive intermittent connectivity.

_This article is a repost of an answer I provided at [programmers.stackexchange.com](http://programmers.stackexchange.com/q/125205/1177)._
