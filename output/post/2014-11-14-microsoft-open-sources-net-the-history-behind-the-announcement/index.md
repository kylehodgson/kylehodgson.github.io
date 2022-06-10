---
title: "Microsoft Open Sources .NET - The History Behind the Announcement"
date: "2014-11-14"
categories: 
  - "technology"
tags: 
  - "net"
  - "open-source"
---

While I'm still surprised at the move, it's not unprecedented - I think they've been letting the open source folks dabble for years now, and they've proven that it works.

- **2007/2008 - MS starts building ASP.NET MVC** While [this](http://en.wikipedia.org/wiki/ASP.NET_MVC_Framework#Release_history) wasn't a move to open source anything, it did finally start to seem as though Microsoft wanted to have a web framework that mattered. ASP.NET WebForms had been terrible for years and wasn't getting any better. StackOverflow becomes an [early success story](http://highscalability.com/stack-overflow-architecture) for ASP.NET MVC.
- **September 2008 - MS starts shipping JQuery alongside ASP.NET AJAX** - While not surprising given the meteoric rise of JQuery, it was still not a foregone conclusion that MS would [start shipping JQuery in Visual Studio](http://weblogs.asp.net/scottgu/jquery-and-microsoft).
- **November 2010 - MS hires Steve Sanderson, author of KnockoutJS** - Personally I always thought [this](http://blog.stevensanderson.com/2010/11/03/hello-microsoft/) was a really cool move - JS frameworks were beginning to be all the rage, and the company I was working at had a lot of success integrating KnockoutJS in to our ASP.NET application. KnockoutJS appealed to MS devs as it used the Model-View-ViewModel pattern that Silverlight and WPF made use of. KnockoutJS was shipping in Visual Studio too. I was a little worried that this would be like [IronRuby](http://en.wikipedia.org/wiki/IronRuby) part two, but it wasn't - KnockoutJS continued to thrive and maintains a respected niche in the wide panoply of JavaScript frameworks.
- **March 2012 - MS open sources ASP.NET MVC:** This one didn't really surprise me, it seemed like a natural fit given MVC's direction at the time. Scott Gu [announced](http://weblogs.asp.net/scottgu/asp-net-mvc-web-api-razor-and-open-source) that ASP.NET MVC, WebAPI and Razor would all be open sourced. My feeling at the time was that Scott Gu, Phil Haack Scott Hanselman (and likely others) were supporting this behind the scenes. I always imagined that Microsoft was trying to win back devs that had left ASP.NET in droves for rails, though I'm not sure what the official line was.
- **July 2012 - MS open sources Entity Framework:** MS ran in to trouble with EntityFramework - for a long time it was less popular than Linq2Sql and nHibernate. While much of the open source .net community would have preferred that they adopt nHibernate, they did eventually [open source EntityFramework](http://weblogs.asp.net/scottgu/entity-framework-and-open-source) and are even proudly [accepting community contributions](http://blogs.msdn.com/b/interoperability/archive/2013/05/31/entity-framework-v6-ef6-beta-1-from-microsoft-includes-latest-amp-greatest-open-source-contributions.aspx). EntityFramework is now a lot less terrible and much more widely used as a result.

Microsoft's moves over the last few years have been decidedly better than the way MS handled [IronPython](http://en.wikipedia.org/wiki/IronPython)/[IronRuby](http://en.wikipedia.org/wiki/IronRuby). The Open Source community has been making steady progress from within.
