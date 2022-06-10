---
title: "4 Indications that HTML5 Mobile Development is Getting Big"
date: "2012-02-15"
categories: 
  - "technology"
---

1. PhoneGap Build is currently touring the podcast circuit, on [Hanselminutes](http://hanselminutes.com/304/making-your-first-phonegap-application-with-peter-mourfield), the [Tablet Show](http://thetabletshow.com/?ShowNum=17), and [DotNetRocks](http://www.dotnetrocks.com/default.aspx?showNum=557). It's not surprising, Adobe's recent acquisition of Nitobi (which built PhoneGap) is quite newsworthy. But really, it's not just that - PhoneGap Build is awesome. So what is it? From my research, it basically helps HTML5 / JavaScript developers to build "native" mobile apps. I have native in quotes, as it won't be the same as developing apps in the main supported environments like Android's Java SDK or Objective C for iPhone. My understanding of it is that PhoneGap basically uses a native browser component, removes the chrome like the back button and address bar, and loads up your HTML5 site from a file:// URL.  Sure, you wouldn't want to build Angry Birds this way, but then again you also don't have to start out by learning an entirely new paradigm. All you have to do is upload your HTML5/javascript/css to build.phonegap.com and they produce binaries for iOS, Android, Blackberry, webOS, and Symbian. They even integrate with Github! Tres cool.
2. jQuery Mobile - this user interface library is a really great way to get started with developing mobile apps. After fooling around with it this weekend for a couple of hours, I quickly had put together a simple product listing application - you can check it out at [www.lcbobuddy.com](http://www.lcbobuddy.com).  You can think of jQuery mobile as some great starter CSS (that works with jQuery theme roller of course!) and a great programming paradigm for developing mobile apps. For instance, there's a really great single page app concept in it too, complete with some basic routing. The idea goes your html file contains multiple <div> elements with a like this:
    
    <div data-role="page" id="product\_listings" >
      <div data-role="header"> <h1>Heading</h1>
      </div><!-- /header -->
      <div data-role="content">
        <ul data-role="listview" data-inset="true" data-filter="true" id="listings\_listview"></ul>
      </div><!-- /content-->
      <div data-role="footer"> <h4>Footer</h4>
      </div><!-- /footer-->
    </div> <!-- /page -->
    <div data-role="page" id="product\_details" >
      <div data-role="header"> <h1>Heading</h1>
      </div><!-- /header -->
      <div data-role="content">
      </div><!-- /content-->
      <div data-role="footer"> <h4>Footer</h4>
      </div><!-- /footer-->
    </div> <!-- /page -->
    
    With this structure in place, you can make links to "#product\_details=1234", and with a simple JavaScript hook you can catch calls to product\_details, parse out the specific product ID and pre-load the product data you need to service the request.
3. [Kendo UI Mobile](http://www.kendoui.com/mobile.aspx) - Telerik has been hard at work on their KendoUI, an open source JavaScript framework. It's in beta now, but the mobile stuff looks really promising. I haven't done any projects with it yet, but Telerik is known for delivering top quality tools. Telerik is a tools vendor - I'm thinking they're building Kendo so that they can sell us tools that make building apps in it easier.
4. Adobe [has been investing in](http://www.adobe.com/solutions/html5.html) HTML5 mobile. Not only did they recently buy Nitobi, but apparently Dreamweaver and Illustrator support mobile development somehow.
