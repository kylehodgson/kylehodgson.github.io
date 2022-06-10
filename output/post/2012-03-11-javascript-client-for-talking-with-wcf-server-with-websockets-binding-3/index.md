---
title: "JavaScript client for talking with WCF server with WebSockets binding"
date: "2012-03-11"
categories: 
  - "technology"
tags: 
  - "c"
  - "javascript"
  - "jquery"
  - "signalr"
  - "wcf"
  - "wcf-ria"
---

_This blog entry is a repost of an answer I wrote at [programmers.stackexchange.com](http://programmers.stackexchange.com/a/139138/1177)._

If you are looking for a JavaScript client for talking with WCF server with WebSockets binding, you might take a look at these two:

1. [SignalR](http://signalr.net/) has a JavaScript API. SignalR helps build asynchronous scalable web applications with real-time persistent long-running connections. Scott Hanselman wrote a great [blog post](http://www.hanselman.com/blog/AsynchronousScalableWebApplicationsWithRealtimePersistentLongrunningConnectionsWithSignalR.aspx) about this.

If that's not your speed, you may be looking for something more like [WCF Support for jQuery](http://wcf.codeplex.com/releases/view/69862), which seems to have sprung up from the old WCF-RIA jQuery client. Looks like this will be part of ASP.NET MVC 4. The[wiki page](http://wcf.codeplex.com/wikipage?title=WCF%20jQuery) for this project seems to indicate that its flexible enough to plug in to WebSockets.

I'm a little concerned about WebSockets in the near term. There are some questions about just how Internet infrastructure (particularly in corporate environments where proxy servers are common) will handle WebSockets. I believe SignalR has strategies to help mitigate this by negotiating fallbacks when WebSockets don't work. Scott Hanselman has another [blog post](http://www.hanselman.com/blog/YourUsersDontCareIfYouUseWebSockets.aspx) about this which makes some good points in this direction.
