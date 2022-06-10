---
title: "JqueryMobile and Service Stack Events Manager Tutorial Post 3"
date: "2012-04-23"
categories: 
  - "technology"
tags: 
  - "net"
  - "jquery"
  - "jquery-mobile"
  - "service-stack"
---

In our previous post, we covered the basics of setting up Visual Studio to support our project – adding Service Stack, removing demo classes we don’t need, and plugging in our basic model and services – we even copied a basic JQueryMobile app.html in to our project. However, we haven’t done anything yet. In today’s post, we’ll poke around and see what we have – then we’ll wire up some JavaScript to make the JQueryMobile app start to connect to the back end.

> Want more on ServiceStack? [ServiceStack 4 Cookbook](http://kylehodgson.com/servicestack-cookbook/) contains over 70 recipes on building quality services and applications with ServiceStack!

Now that you can display the app.html in a browser, let’s try something else: visit the URL /metadata/. You should see something like the following:

![images/ss-3-1.png](images/ss-3-1.png)

ServiceStack has created a helpful metadata page with links to XSD, WSDL, and other documentation for connecting to your service, including links to documentation on the ServiceStack page on how to create one line clients in C#. You can see we have an Events operation that’s linked to our EventsService. View the JSON link shows us documentation of how to call the Events service via JavaScript, which boils down connecting to /events?format=json. This should be easy to hit with JavaScript!

Before we can continue we’re going to need some dummy data. In my project I’ve created an EventRepository that creates some fake event data for us on the fly with a simple for loop as per below:

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">enum</span></b> EventRepositoryResponse
<span style="color:#ff0000;">{</span>
    Success<span style="color:#990000;">,</span>
    DateAlreadyPresent
<span style="color:#ff0000;">}</span>
<div></div>
<b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">EventRepository</span>
<span style="color:#ff0000;">{</span>
    <b><span style="color:#0000ff;">private</span></b> <span style="color:#008080;">List&lt;Event&gt;</span> _repo<span style="color:#990000;">;</span>
    <b><span style="color:#0000ff;">public</span></b> <b><span style="color:#000000;">EventRepository</span></b><span style="color:#990000;">()</span>
    <span style="color:#ff0000;">{</span>
        _repo <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> List<span style="color:#990000;">&lt;</span>Event<span style="color:#990000;">&gt;();</span>
        <b><span style="color:#000000;">InitializeTestData</span></b><span style="color:#990000;">();</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">public</span></b> <b><span style="color:#000000;">EventRepository</span></b><span style="color:#990000;">(</span><span style="color:#009900;">object</span> repo<span style="color:#990000;">)</span>
    <span style="color:#ff0000;">{</span>
        _repo <span style="color:#990000;">=</span> repo <span style="color:#008080;">as</span> List<span style="color:#990000;">&lt;</span>Event<span style="color:#990000;">&gt;;</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">internal</span></b> <span style="color:#008080;">List&lt;Event&gt;</span> <b><span style="color:#000000;">GetEvents</span></b><span style="color:#990000;">()</span>
    <span style="color:#ff0000;">{</span>
        <b><span style="color:#0000ff;">return</span></b> _repo<span style="color:#990000;">;</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">internal</span></b> <span style="color:#008080;">Event</span> <b><span style="color:#000000;">GetEvent</span></b><span style="color:#990000;">(</span><span style="color:#009900;">string</span> subject<span style="color:#990000;">)</span>
    <span style="color:#ff0000;">{</span>
        <b><span style="color:#0000ff;">return</span></b> <span style="color:#990000;">(</span>_repo<span style="color:#990000;">.</span><b><span style="color:#000000;">Where</span></b><span style="color:#990000;">(</span>e <span style="color:#990000;">=&gt;</span> e<span style="color:#990000;">.</span>Subject<span style="color:#990000;">.</span><b><span style="color:#000000;">ToLower</span></b><span style="color:#990000;">()</span> <span style="color:#990000;">==</span> subject<span style="color:#990000;">.</span><b><span style="color:#000000;">ToLower</span></b><span style="color:#990000;">())).</span><b><span style="color:#000000;">FirstOrDefault</span></b><span style="color:#990000;">();</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">internal</span></b> <span style="color:#008080;">EventRepositoryResponse</span> <b><span style="color:#000000;">SaveEvent</span></b><span style="color:#990000;">(</span><span style="color:#008080;">Event</span> eve<span style="color:#990000;">)</span>
    <span style="color:#ff0000;">{</span>
        <span style="color:#008080;">var</span> count <span style="color:#990000;">=</span> <span style="color:#990000;">(</span>from <span style="color:#008080;">e</span> <b><span style="color:#0000ff;">in</span></b> _repo <span style="color:#008080;">where</span> e<span style="color:#990000;">.</span>EventStart <span style="color:#990000;">==</span> eve<span style="color:#990000;">.</span>EventStart <span style="color:#008080;">select</span> e<span style="color:#990000;">).</span><b><span style="color:#000000;">Count</span></b><span style="color:#990000;">();</span>
        <b><span style="color:#0000ff;">if</span></b> <span style="color:#990000;">(</span>count <span style="color:#990000;">&gt;</span> <span style="color:#993399;">0</span><span style="color:#990000;">)</span> <b><span style="color:#0000ff;">return</span></b> EventRepositoryResponse<span style="color:#990000;">.</span>DateAlreadyPresent<span style="color:#990000;">;</span>
        _repo<span style="color:#990000;">.</span><b><span style="color:#000000;">Add</span></b><span style="color:#990000;">(</span>eve<span style="color:#990000;">);</span>
        <b><span style="color:#0000ff;">return</span></b> EventRepositoryResponse<span style="color:#990000;">.</span>Success<span style="color:#990000;">;</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">internal</span></b> <span style="color:#009900;">int</span> <b><span style="color:#000000;">GetCountOfEvents</span></b><span style="color:#990000;">()</span>
    <span style="color:#ff0000;">{</span>
        <b><span style="color:#0000ff;">return</span></b> <b><span style="color:#000000;">GetCountOfEvents</span></b><span style="color:#990000;">(</span><b><span style="color:#0000ff;">null</span></b><span style="color:#990000;">);</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">internal</span></b> <span style="color:#009900;">int</span> <b><span style="color:#000000;">GetCountOfEvents</span></b><span style="color:#990000;">(</span><span style="color:#009900;">string</span> searchTerm<span style="color:#990000;">)</span>
    <span style="color:#ff0000;">{</span>
        <b><span style="color:#0000ff;">if</span></b> <span style="color:#990000;">(</span>searchTerm <span style="color:#990000;">==</span> <b><span style="color:#0000ff;">null</span></b><span style="color:#990000;">)</span> <b><span style="color:#0000ff;">return</span></b> _repo<span style="color:#990000;">.</span>Count<span style="color:#990000;">;</span>
        <b><span style="color:#0000ff;">return</span></b> _repo<span style="color:#990000;">.</span><b><span style="color:#000000;">Count</span></b><span style="color:#990000;">(</span>e <span style="color:#990000;">=&gt;</span> <span style="color:#990000;">(</span>e<span style="color:#990000;">.</span>Subject<span style="color:#990000;">.</span><b><span style="color:#000000;">Contains</span></b><span style="color:#990000;">(</span>searchTerm<span style="color:#990000;">)));</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">internal</span></b> <span style="color:#009900;">void</span> <b><span style="color:#000000;">InitializeTestData</span></b><span style="color:#990000;">()</span>
    <span style="color:#ff0000;">{</span>
        <span style="color:#009900;">int</span> recordsToMake <span style="color:#990000;">=</span> <span style="color:#993399;">20</span><span style="color:#990000;">;</span>
        <b><span style="color:#0000ff;">for</span></b> <span style="color:#990000;">(</span><span style="color:#009900;">int</span> i <span style="color:#990000;">=</span> <span style="color:#993399;">1</span><span style="color:#990000;">;</span> i <span style="color:#990000;">&lt;</span> recordsToMake<span style="color:#990000;">;</span> i<span style="color:#990000;">++)</span>
        <span style="color:#ff0000;">{</span>
            _repo<span style="color:#990000;">.</span><b><span style="color:#000000;">Add</span></b><span style="color:#990000;">(</span>
                <b><span style="color:#0000ff;">new</span></b> Event
                <span style="color:#ff0000;">{</span>
                    EventStart <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> <b><span style="color:#000000;">DateTime</span></b><span style="color:#990000;">(</span><span style="color:#993399;">2012</span><span style="color:#990000;">,</span> <span style="color:#993399;">4</span><span style="color:#990000;">,</span> i<span style="color:#990000;">),</span>
                    Speaker <span style="color:#990000;">=</span> <span style="color:#ff0000;">"Atley Hunter"</span><span style="color:#990000;">,</span>
                    Subject <span style="color:#990000;">=</span> <span style="color:#ff0000;">"WP7 Programming"</span>
                <span style="color:#ff0000;">}</span><span style="color:#990000;">);</span>
        <span style="color:#ff0000;">}</span>
    <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

Now we can wire that up to our EventsService like so:

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt><b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">class</span></b> <span style="color:#008080;">EventsService</span> <span style="color:#990000;">:</span> RestServiceBase<span style="color:#990000;">&lt;</span>EventRequest<span style="color:#990000;">&gt;</span>
<span style="color:#ff0000;">{</span>
    <b><span style="color:#0000ff;">private</span></b> <span style="color:#008080;">EventRepository</span> repository<span style="color:#990000;">;</span>
    <b><span style="color:#0000ff;">public</span></b> <b><span style="color:#000000;">EventsService</span></b><span style="color:#990000;">()</span>
    <span style="color:#ff0000;">{</span>
        repository <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> <b><span style="color:#000000;">EventRepository</span></b><span style="color:#990000;">();</span>
    <span style="color:#ff0000;">}</span>
    <b><span style="color:#0000ff;">public</span></b> <b><span style="color:#000000;">EventsService</span></b><span style="color:#990000;">(</span><span style="color:#008080;">EventRepository</span> repo<span style="color:#990000;">)</span>
    <span style="color:#ff0000;">{</span>
        repository <span style="color:#990000;">=</span> repo<span style="color:#990000;">;</span>
    <span style="color:#ff0000;">}</span>
<div></div>
    <b><span style="color:#0000ff;">public</span></b> <b><span style="color:#0000ff;">override</span></b> <span style="color:#009900;">object</span> <b><span style="color:#000000;">OnGet</span></b><span style="color:#990000;">(</span><span style="color:#008080;">EventRequest</span> request<span style="color:#990000;">)</span>
    <span style="color:#ff0000;">{</span>
        <span style="color:#008080;">var</span> list <span style="color:#990000;">=</span> repository<span style="color:#990000;">.</span><b><span style="color:#000000;">GetEvents</span></b><span style="color:#990000;">();</span>
        <span style="color:#008080;">var</span> output <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> EventResponse
        <span style="color:#ff0000;">{</span>
            EventListings <span style="color:#990000;">=</span> list
        <span style="color:#ff0000;">}</span><span style="color:#990000;">;</span>
<div></div>
        <b><span style="color:#0000ff;">return</span></b> output<span style="color:#990000;">;</span>
    <span style="color:#ff0000;">}</span>
<span style="color:#ff0000;">}</span></tt></pre></td></tr></tbody></table>

We’re not doing paging yet, but that’ll come soon enough. Before we can work with the EventRepository we need to adjust AppHost again, by adding the following line to Configure:

container.Register(new EventRepository());

After doing this, we should be able to hit the /events/ URL and see something like the following, thanks to ServiceStack’s built in HTML5 data viewer:

![images/ss-3-2.png](images/ss-3-2.png)

So, now we know our repository is wired up and working. Next, we just need some simple JavaScript to get the app.html using this data source. Codiqa should have created a <script> block at the bottom of the app.html page. Find it, and plug in the following :

<table border="0" width="100%" cellpadding="10" bgcolor="#e8e8e8"><tbody><tr><td><pre><tt>$<span style="color:#990000;">(</span>document<span style="color:#990000;">).</span><b><span style="color:#000000;">ready</span></b><span style="color:#990000;">(</span><b><span style="color:#0000ff;">function</span></b> <span style="color:#990000;">()</span> <span style="color:#ff0000;">{</span>
<div></div>
    jQuery<span style="color:#990000;">.</span><b><span style="color:#000000;">getJSON</span></b><span style="color:#990000;">(</span><span style="color:#ff0000;">"/events?format=json"</span><span style="color:#990000;">,</span>
<div></div>
        <b><span style="color:#0000ff;">function</span></b> <span style="color:#990000;">(</span>data<span style="color:#990000;">)</span> <span style="color:#ff0000;">{</span>
<div></div>
            <b><span style="color:#0000ff;">for</span></b> <span style="color:#990000;">(</span><b><span style="color:#0000ff;">var</span></b> idx <b><span style="color:#0000ff;">in</span></b> data<span style="color:#990000;">.</span>eventListings<span style="color:#990000;">)</span> <span style="color:#ff0000;">{</span>
<div></div>
                <b><span style="color:#0000ff;">var</span></b> listing <span style="color:#990000;">=</span> data<span style="color:#990000;">.</span>eventListings<span style="color:#990000;">[</span>idx<span style="color:#990000;">];</span>
                <b><span style="color:#0000ff;">var</span></b> date <span style="color:#990000;">=</span> <b><span style="color:#0000ff;">new</span></b> <b><span style="color:#000000;">Date</span></b><span style="color:#990000;">(</span><b><span style="color:#000000;">parseInt</span></b><span style="color:#990000;">(</span>listing<span style="color:#990000;">.</span>eventStart<span style="color:#990000;">.</span><b><span style="color:#000000;">substr</span></b><span style="color:#990000;">(</span><span style="color:#993399;">6</span><span style="color:#990000;">)));</span>
<div></div>
                $<span style="color:#990000;">(</span><span style="color:#ff0000;">"#listview_events"</span><span style="color:#990000;">).</span><b><span style="color:#000000;">append</span></b><span style="color:#990000;">(</span>
                                        <span style="color:#ff0000;">'&lt;li data-theme="c"&gt;'</span> <span style="color:#990000;">+</span>
                                        <span style="color:#ff0000;">'&lt;a href="#" data-transition="slide"&gt;'</span> <span style="color:#990000;">+</span> listing<span style="color:#990000;">.</span>speaker
                                        <span style="color:#990000;">+</span> <span style="color:#ff0000;">' presents '</span> <span style="color:#990000;">+</span> listing<span style="color:#990000;">.</span>subject <span style="color:#990000;">+</span> <span style="color:#ff0000;">' '</span>
                                        <span style="color:#990000;">+</span> date<span style="color:#990000;">.</span><b><span style="color:#000000;">toDateString</span></b><span style="color:#990000;">()</span>  <span style="color:#990000;">+</span> <span style="color:#ff0000;">'&lt;/a&gt;&lt;/li&gt;'</span><span style="color:#990000;">);</span>
            <span style="color:#ff0000;">}</span>
<div></div>
            $<span style="color:#990000;">(</span><span style="color:#ff0000;">"#listview_events"</span><span style="color:#990000;">).</span><b><span style="color:#000000;">listview</span></b><span style="color:#990000;">(</span><span style="color:#ff0000;">'refresh'</span><span style="color:#990000;">);</span>
        <span style="color:#ff0000;">}</span><span style="color:#990000;">);</span>
<span style="color:#ff0000;">}</span><span style="color:#990000;">);</span></tt></pre></td></tr></tbody></table>

For this to work, you’re going to have to locate the listview in app.html and add an id to it. In the example above, I chose listview\_events. What this tells the page to do is connect to our local events URL, request it in JSON format, then add a list item to the listview for each one. Once finished, we call listview(‘refresh’) so that JQueryMobile can work its magic and apply styling and functionality to the list. With all of this in place, if we view app.html again we should see the following:

![images/ss-3-3.png](images/ss-3-3.png)

Voila, we have a basic service back end and a very basic HTML5 mobile app talking to it.

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><b><span style="text-decoration:underline;">Note</span></b></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><b>Service Stack Series</b><div></div>This article is one of a three part series on Service Stack. <a href="http://kylehodgson.com/tag/service-stack/">View All Articles</a> <a href="https://github.com/kylehodgson/servicestacktutorial">View Complete Source Code</a></td></tr></tbody></table>
