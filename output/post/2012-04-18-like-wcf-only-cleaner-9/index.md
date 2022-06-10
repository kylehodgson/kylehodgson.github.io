---
title: "Like WCF: Only Cleaner"
date: "2012-04-18"
categories: 
  - "technology"
tags: 
  - "net"
  - "service-stack"
  - "wcf"
---

I’ve been working with the [Service Stack](http://www.servicestack.net) framework recently. Service Stack is a great SOA framework for building REST (and RPC) based web services - lots of the great things about WCF are in there - but they fixed lots of the problems. The thing that initially attracted me to it, is that it does what WCF does without all the XML configuration. I know projects whose XML file count has gone way up when they start using WCF. In one extreme case, a project that wanted a sustainable development environment with different configs for development, staging, production, and qa but also wanted separate files for endpoints, behaviours and services ended up with over 500 XML files in trunk. It takes a zen like state to manage all of this, and unfortunately it takes hours to reach that zen!

> Want more on ServiceStack? [ServiceStack 4 Cookbook](http://kylehodgson.com/servicestack-cookbook/) contains over 70 recipes on building quality services and applications with ServiceStack!

Service stack could have just been "WCF without the mess" and I would have loved it, but they didn’t stop there - they focused on doing it better and faster. The [benchmarks](http://www.servicestack.net/benchmarks/) tell the story. Their JSON and XML serializers are 3 to 4 times faster than the Microsoft components in WCF! Add to that Service Stack is much more testable.

So, you might ask, what does the code look like? You can implement a very testable service in very little code. We’ll start out with a request object and object model like this:

**Event**

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">Event</span>
<span style="color:#ff0000;">{</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">string</span> Subject <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#008080;">DateTime</span> EventDate <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">string</span> Speaker <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#008080;">DateTime</span> EventStart <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

**EventRequest**

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">EventRequest</span>
<span style="color:#ff0000;">{</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">int</span><span style="color:#990000;">?</span> Page <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">string</span> SearchTerm <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

Then we’ll start a test:

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><span style="color:#990000;">[</span>TestMethod<span style="color:#990000;">]</span>
<b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">void</span> <b><span style="color:#000000;">CanIDownloadAListOfEvents</span></b><span style="color:#990000;">()</span>
<span style="color:#ff0000;">{</span>
  <span style="color:#008080;">var</span> request <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> <b><span style="color:#000000;">EventRequest</span></b><span style="color:#990000;">();</span>
  <span style="color:#008080;">var</span> target <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> <b><span style="color:#000000;">EventsService</span></b><span style="color:#990000;">();</span>
  <span style="color:#008080;">var</span> actual <span style="color:#990000;">=</span> target<span style="color:#990000;">.</span><b><span style="color:#000000;">OnGet</span></b><span style="color:#990000;">(</span>request<span style="color:#990000;">);</span>
  Assert<span style="color:#990000;">.</span><b><span style="color:#000000;">IsNotNull</span></b><span style="color:#990000;">(</span>actual<span style="color:#990000;">);</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

EventsService is red because we haven’t made one yet, so let’s add one of those:

**EventService**

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">EventsService</span> <span style="color:#990000;">:</span> RestServiceBase<span style="color:#990000;">&lt;</span>EventRequest<span style="color:#990000;">&gt;</span>
<span style="color:#ff0000;">{</span>
  <b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">override</span></b> <span style="color:#009900;">object</span> <b><span style="color:#000000;">OnGet</span></b><span style="color:#990000;">(</span><span style="color:#008080;">EventRequest</span> request<span style="color:#990000;">)</span>
  <span style="color:#ff0000;">{</span>
    <b><span style="color:#0000ff;">return</span></b> <b><span style="color:#0000ff;">new</span></b> <b><span style="color:#000000;">EventResponse</span></b><span style="color:#990000;">();</span>
  <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

Here’s where Service Stack does some magic… To wire up a service on that Events request object, we just need to annotate it properly:

**EventRequest**

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><span style="color:#990000;">[</span>DataContract<span style="color:#990000;">]</span>
<span style="color:#990000;">[</span><b><span style="color:#000000;">RestService</span></b><span style="color:#990000;">(</span><span style="color:#ff0000;">"/events"</span><span style="color:#990000;">,</span> <span style="color:#ff0000;">"GET"</span><span style="color:#990000;">)]</span>
<span style="color:#990000;">[</span><b><span style="color:#000000;">RestService</span></b><span style="color:#990000;">(</span><span style="color:#ff0000;">"/events/page/{Page}"</span><span style="color:#990000;">,</span> <span style="color:#ff0000;">"GET"</span><span style="color:#990000;">)]</span>
<span style="color:#990000;">[</span><b><span style="color:#000000;">RestService</span></b><span style="color:#990000;">(</span><span style="color:#ff0000;">"/events/search/{SearchTerm}"</span><span style="color:#990000;">,</span> <span style="color:#ff0000;">"GET"</span><span style="color:#990000;">)]</span>
<b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">EventRequest</span>
<span style="color:#ff0000;">{</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">int</span><span style="color:#990000;">?</span> Page <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">string</span> SearchTerm <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

The RestService annotation is telling ServiceStack "put it here, and make the URL’s like this". Based on the above, calls to /events/page/2 will pass in the number 2 for Page, and calls to /events/search/foo will pass in the search term foo. Now we should create our response object:

**EventResponse**

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><span style="color:#990000;">[</span>DataContract<span style="color:#990000;">]</span>
<b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">EventResponse</span>
<span style="color:#ff0000;">{</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#008080;">IList&lt;Event&gt;</span> EventListings <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">int</span> RecordsPerPage <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">int</span> CurrentPage <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">int</span><span style="color:#990000;">?</span> NextPage <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
    <span style="color:#990000;">[</span>DataMember<span style="color:#990000;">]</span>
    <b><span style="color:#0000ff;">public</span></b> <span style="color:#009900;">bool</span> IsLastPage <span style="color:#ff0000;">{</span> <b><span style="color:#0000ff;">get</span></b><span style="color:#990000;">;</span> <b><span style="color:#0000ff;">set</span></b><span style="color:#990000;">;</span> <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

And that’s it! At this point your test should pass1, and not only this but if you visit _/metadata_ you’ll see that ServiceStack has nailed up REST, CSV, JSV, JSON and SOAP services for you.

1 there are hosting details to work out as well, but it’s minimal. Check out the examples at [www.servicestack.net](http://www.servicestack.net) for more details or [view the complete project](https://github.com/kylehodgson/servicestacktutorial)

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><b><span style="text-decoration:underline;">Note</span></b></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><b>Service Stack Series</b><div></div>This article is one of a three part series on Service Stack. <a href="http://kylehodgson.com/tag/service-stack/">View All Articles</a> <a href="https://github.com/kylehodgson/servicestacktutorial">View Complete Source Code</a></td></tr></tbody></table>
