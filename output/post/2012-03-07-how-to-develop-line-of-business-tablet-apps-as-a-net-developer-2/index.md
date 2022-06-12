---
title: "How to develop line-of-business tablet apps as a .NET developer?"
date: "2012-03-07"
categories: 
  - "technology"
tags: 
  - "html5"
  - "mobile"
  - "phonegap"
---

> _Outdated content warning._ The techniques in this article are now out of date. I'd be looking towards something like flutter or React Native today.

_This blog post is a repost of an answer I wrote at [programmers.stackexchange.com](http://programmers.stackexchange.com/questions/138650)_

A great way for a .NET developer to build line of business tablet apps that target Android or even iOS, is to leverage [JQuery Mobile](http://jquerymobile.com/) + [Phone Gap Build](http://build.phonegap.com/).

Nitobi's [Phone Gap Build](http://build.phonegap.com/) service (now owned by Adobe) allows developers to convert HTML5/JavaScript apps to "native" apps (really hybrid apps) that can be deployed locally to a device. It's my understanding that basically what's happening under the hood is packaging a small native binary that calls the native browser and loads up your site from a file:// URL.

You don't need to target any specific JavaScript framework - the same HTML & JavaScript that would work really well in a mobile web app will work fine.

Offline support isn't hard, either. With local browser storage well supported with many mobile devices you can build truly powerful offline apps this way. Its best practice to package your external dependencies locally as opposed to using a CDN, so that your app works well offline.

Frameworks like [KnockoutJS](http://knockoutjs.com/) and [BackboneJS](http://documentcloud.github.com/backbone/) are very helpful at allowing you to build well engineered JavaScript apps, and they work just fine with Phone Gap's build service.

When the device is online, you can easily have it hit ASP.NET/MVC, WebAPI or WCF service back ends to refresh data.

The resulting apps are really quite good, and can be distributed in the Apple and Android markets. There are already lots of apps in those marketplaces built with Phone Gap Build and other similar products, and 99% of people (including most devs) can't tell the difference.

Obviously you aren't going to try to build Angry Birds this way (though, with Canvas I suppose you might try), it works wonderfully well with the kinds of apps you are talking about.

Don't take my word for it. PhoneGap has been doing the rounds on the podcast circuit, having recently been on [Hanselminutes](http://www.hanselminutes.com/304/making-your-first-phonegap-application-with-peter-mourfield), [DotNetRocks](http://www.dotnetrocks.com/default.aspx?showNum=557), and the [Tablet Show](http://thetabletshow.com/?ShowNum=17).
