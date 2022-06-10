---
title: "On Microsoft, HTML5, and Silverlight"
date: "2010-10-30"
categories: 
  - "technology"
tags: 
  - "html5"
  - "silverlight"
---

I don' t want to get in to the big picture "Adobe vs. HTML5 vs. Silverlight" discussion, I think its been done well elsewhere.

However, if Mary Jo is right in her [recent article](http://www.zdnet.com/blog/microsoft/microsoft-our-strategy-with-silverlight-has-shifted/7834) about Microsoft's intentions, I'm a little worried:

> “Silverlight is our development platform for Windows Phone,” he said. Silverlight also has some “sweet spots” in media and line-of-business applications, he said.
> 
> But when it comes to touting Silverlight as Microsoft’s vehicle for delivering a cross-platform runtime, “our strategy has shifted,” Muglia told me.

and later:

> Muglia didn’t share any kind of timetable as to when Silverlight 5 might make its debut. He did note that the delivery pace of Silverlight is slowing. “As with anything as it matures, the (delivery) cadence changes,” he said.

That last bit sends chills down my spine.  The trouble is, I've been observing Silverlight's growth trajectory, and watching it mature as an excellent potential platform for line of business apps. As a SaaS ISV delivering, among other things, accounting applications for large organizations, Silverlight's mix of easy deployment, rapid application development, and inherent cross platform nature make my life easy. We recently undertook a project to replace an aging ASP.NET WebForms app containing some 1,500 lines of pre-JQuery JavaScript with a Silverlight app, and we're thrilled. Better developer productivity, a very clean UI... I realize that Silverlight might not be considered "the web"; but it is in my mind an Internet platform for rapid application development that delivers apps on all major operating systems elegantly.

I don't want the world to ignore HTML5, and I'm really excited about the "new JavaScript" world - node.js, CouchDB and others are all doing things in JavaScript that are really interesting.  Projects that we deliver to end consumers in their homes, of course we want HTML5 there. I don't want to be beholden to a plugin when I'm targeting end consumers. But when we're delivering line of business software to a large organization that can help us make sure that Silverlight is on all of the machines we are licensing, Silverlight is a real winner.

So, if Microsoft is "embracing HTML5" why am I worried?

A decade of Microsoft's ASP.NET WebForms world leaves me a little dubious of Microsoft's commitment to well formed, cross browser HTML. When I watch a Silverlight developer add a button control in XAML or even drag it on to a Silverlight form, I'm not worried about the mess being made. I can't say the same thing about WebForms. Micosoft's historical tooling support for HTML CSS and JavaScript is just not all that good; and what's to say they won't try to use HTML5 in an embrace and extend campaign to win IE9 marketshare back?

Of course, there's ASP.NET/MVC, and of course we love it for consumer facing web apps - but it makes assumptions that the developer can deliver good markup, which will need to be tested on IE7, IE8, IE9, FF2, FF3, FF3.5, Chrome, Safari... need I go on? Of course JQuery makes that easier than it was, no doubt - but I think what I liked about Silverlight is that I could put a good developer who knows XAML and C# well, sit him down in a chair with a good set of tools, and out would come easily managed well formed code. To do a good job in ASP.NET/MVC the developer needs to be expert in a much longer list of technologies.

If I had my way, Microsoft's IE9 would do a good job supporting HTML5 - but Microsoft would continue to invest in developing Silverlight, and not just for the phone. I guess I'm just afraid of how far they're going to turn the ship...

_**UPDATE:**_

A [recent Microsoft publication](http://team.silverlight.net/announcement/pdc-and-silverlight/) addresses some of the concerns above and makes for good reading on the future of Silverlight.
