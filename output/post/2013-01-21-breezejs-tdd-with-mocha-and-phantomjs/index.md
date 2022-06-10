---
title: "BreezeJS: TDD With Mocha and PhantomJS"
date: "2013-01-21"
categories: 
  - "agile"
  - "technology"
tags: 
  - "breezejs"
  - "javascript"
  - "mochajs"
  - "phantomjs"
  - "tdd"
---

I’m really interested in [BreezeJS](http://www.breezejs.com/) right now. It seems like a really promising new [open source](https://github.com/IdeaBlade/Breeze) (MIT License) framework for helping you get data into your JavaScript applications, including single page applications (which are coming to be known by the acronym SPA).

Breeze has lots of features. One to whet your appetite is the query syntax:

**BreezeJS Queries**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">var</font></b> query <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> breeze<font color="#990000">.</font><b><font color="#000000">EntityQuery</font></b><font color="#990000">()</font>
  <font color="#990000">.</font><b><font color="#000000">from</font></b><font color="#990000">(</font><font color="#FF0000">"Employees"</font><font color="#990000">)</font>
  <font color="#990000">.</font><b><font color="#000000">where</font></b><font color="#990000">(</font><font color="#FF0000">"LastName"</font><font color="#990000">,</font> <font color="#FF0000">"startsWith"</font><font color="#990000">,</font> <font color="#FF0000">"P"</font><font color="#990000">)</font>
  <font color="#990000">.</font><b><font color="#000000">orderBy</font></b><font color="#990000">(</font><font color="#FF0000">"LastName"</font><font color="#990000">);</font></tt></pre></td></tr></tbody></table>

I’m attracted to the fluent aproach; and to the idea that you can build up queries client side that interact intelligently with a back end. Under the covers, this filter is being passed to a remote facade that can interpret the query - we aren’t downloading all of /api/Employees/ to do this. Breeze achieves this by interacting with remote [OData](http://en.wikipedia.org/wiki/Open_Data_Protocol) [endpoints](http://www.odata.org/libraries).

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><p><b>OData</b></p>According to Wikipedia, the Open Data Protocol (a.k.a OData) is a data access protocol from Microsoft. It is similar to JDBC and ODBC although OData is not limited to SQL databases. OData is built on the Atom Publishing Protocol and JSON where the Atom structure is the envelope that contains the data returned from each OData request. An OData request uses the REST model for all requests.</td></tr></tbody></table>

I’d like to explore using BreezeJS with a JVM language. There is a Java library for producing and consuming OData called [odata4j](http://code.google.com/p/odata4j/) which I’m excited about. It’s my practice to start with a failing test - so step one is to get Breeze in a working test harness. If this goes well, we’ll keep going and try to connect it with odata4j. Though the [Breeze project uses qunit](http://www.breezejs.com/documentation/testing-breeze-application-0), I’ve heard good things about Mocha. Early tests indicate that BreezeJS references window, so I’ll need something like [mocha-phantomjs](https://github.com/metaskills/mocha-phantomjs). The mocha-phantomjs GitHub readme talks about using a project called [ChaiJS](http://chaijs.com/) for assertions, so I’ll pull that in as well. The steps I used to get mocha up and running with breeze are below. While I don’t intend to use NodeJS in this tutorial, it seems like the quickest way to get Mocha going is to use Node’s npm package manager.

**Installing Mocha And Breeze Dependencies**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt>brew install phantomjs
brew install node
mkdir lib<font color="#990000">;</font> cd lib
npm install mocha
npm install chai
npm install q
npm install mocha-phantomjs</tt></pre></td></tr></tbody></table>

Breeze can’t be installed via npm, of course. The BreezeJS project has put a lot of effort in to making sure that the nuget path works smoothly, but I don’t have nuget on my Mac. There is a zip file to download, however, if you check their [download page](http://www.breezejs.com/). I downloaded [runtime 0.85.2](http://www.breezejs.com/sites/all/packages/breeze-runtime-0.85.2.zip), and unzipped it.

**Installing BreezeJS**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt>mkdir breezejs
cd breezejs
wget http<font color="#990000">:</font>//www<font color="#990000">.</font>breezejs<font color="#990000">.</font>com/sites/all/packages/breeze-runtime-<font color="#993399">0.85</font><font color="#990000">.</font><font color="#993399">2</font><font color="#990000">.</font>zip
unzip breeze-runtime-<font color="#993399">0.85</font><font color="#990000">.</font><font color="#993399">2</font><font color="#990000">.</font>zip
rm -Rf WebApi<font color="#990000">/</font></tt></pre></td></tr></tbody></table>

* * *

## Assembling the Test Harness

Let’s start with a test runner and a bare bones test that proves that we can access Breeze. Here’s my working test-runner:

**Test Runner _test/JavaScript/test-runner.html_**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">&lt;html&gt;</font></b>
  <b><font color="#0000FF">&lt;head&gt;</font></b>
    <b><font color="#0000FF">&lt;meta</font></b> <font color="#009900">charset</font><font color="#990000">=</font><font color="#FF0000">"utf-8"</font><b><font color="#0000FF">&gt;</font></b>
    <b><font color="#0000FF">&lt;link</font></b> <font color="#009900">rel</font><font color="#990000">=</font><font color="#FF0000">"stylesheet"</font> <font color="#009900">href</font><font color="#990000">=</font><font color="#FF0000">"../../lib/node_modules/mocha/mocha.css"</font> <b><font color="#0000FF">/&gt;</font></b>
  <b><font color="#0000FF">&lt;/head&gt;</font></b>
  <b><font color="#0000FF">&lt;body&gt;</font></b>
    <b><font color="#0000FF">&lt;div</font></b> <font color="#009900">id</font><font color="#990000">=</font><font color="#FF0000">"mocha"</font><b><font color="#0000FF">&gt;&lt;/div&gt;</font></b>
<div></div>
    <i><font color="#9A1900">&lt;!-- infrastructure and dependencies --&gt;</font></i>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font>
      <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"</font> <b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"../../lib/node_modules/mocha/mocha.js"</font> <b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"../../lib/node_modules/chai/chai.js"</font> <b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"../../lib/node_modules/q/q.js"</font> <b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"../../lib/breezejs/Scripts/breeze.js"</font> <b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<div></div>
    <i><font color="#9A1900">&lt;!-- setting up mocha and chai --&gt;</font></i>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><b><font color="#0000FF">&gt;</font></b>
      mocha<font color="#990000">.</font><b><font color="#000000">ui</font></b><font color="#990000">(</font><font color="#FF0000">'bdd'</font><font color="#990000">);</font>
      mocha<font color="#990000">.</font><b><font color="#000000">reporter</font></b><font color="#990000">(</font><font color="#FF0000">'html'</font><font color="#990000">);</font>
      expect <font color="#990000">=</font> chai<font color="#990000">.</font>expect<font color="#990000">;</font>
      assert <font color="#990000">=</font> chai<font color="#990000">.</font>assert<font color="#990000">;</font>
    <b><font color="#0000FF">&lt;/script&gt;</font></b>
<div></div>
    <i><font color="#9A1900">&lt;!-- include our specifications --&gt;</font></i>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"breeze-dependencies.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"TestNetflixClient.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<div></div>
    <i><font color="#9A1900">&lt;!-- run the tests --&gt;</font></i>
    <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><b><font color="#0000FF">&gt;</font></b>
      <b><font color="#0000FF">if</font></b> <font color="#990000">(</font>window<font color="#990000">.</font>mochaPhantomJS<font color="#990000">)</font> <font color="#FF0000">{</font> mochaPhantomJS<font color="#990000">.</font><b><font color="#000000">run</font></b><font color="#990000">();</font> <font color="#FF0000">}</font>
      <b><font color="#0000FF">else</font></b> <font color="#FF0000">{</font> mocha<font color="#990000">.</font><b><font color="#000000">run</font></b><font color="#990000">();</font> <font color="#FF0000">}</font>
    <b><font color="#0000FF">&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;/body&gt;</font></b>
<b><font color="#0000FF">&lt;/html&gt;</font></b></tt></pre></td></tr></tbody></table>

**Specification _test/JavaScript/breeze-dependencies.js_**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#000000">describe</font></b><font color="#990000">(</font><font color="#FF0000">'NetflixClient'</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
    <b><font color="#000000">it</font></b><font color="#990000">(</font><font color="#FF0000">'should be able to use BreezeJS'</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
            <b><font color="#0000FF">var</font></b> manager <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> breeze<font color="#990000">.</font><b><font color="#000000">EntityManager</font></b><font color="#990000">(</font><font color="#FF0000">{</font>
                    serviceName<font color="#990000">:</font> <font color="#FF0000">"http://odata.netflix.com/v2/Catalog/"</font>
                <font color="#FF0000">}</font><font color="#990000">);</font>
            <b><font color="#0000FF">var</font></b> query <font color="#990000">=</font> breeze<font color="#990000">.</font>EntityQuery<font color="#990000">.</font><b><font color="#000000">from</font></b><font color="#990000">(</font><font color="#FF0000">"Products"</font><font color="#990000">);</font>
            <b><font color="#0000FF">return</font></b> manager<font color="#990000">.</font><b><font color="#000000">executeQuery</font></b><font color="#990000">(</font>query<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">)</font>
<font color="#FF0000">}</font><font color="#990000">);</font></tt></pre></td></tr></tbody></table>

An astute reader will notice that this test doesn’t do much, it just makes sure that we have Breeze in a harness. Now, we can run this test on the command line like so to see if our tests pass.

<table border="0" bgcolor="#e8e8e8" width="100%" style="margin:.2em 0;"><tbody><tr><td style="padding:.5em;"><pre style="margin:0;padding:0;">$ mocha-phantomjs test/JavaScript/test-runner.html
<div></div>

  NetflixClient
    ✓ should be able to use BreezeJS
<div></div>

  1 test complete (18 ms)</pre></td></tr></tbody></table>

Excellent! We have a workable test environment.

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Tip</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><p><b>Debugging</b></p>To get the test-runner set up how I wanted, I often opened it in Chrome so that I could read the console to help troubleshoot issues. This worked fine with one exception: BreezeJS is trying to make XmlHttpRequests back to a live OData endpoint, but you’ve loaded the page from a file:// URL. In this case, your origin is null, and Chrome’s CORS rules prevent you from accessing the endpoint. While it would be a really bad idea to do this all the time, when you need debugging you can start Chrome with this command to alleviate this issue:</td></tr></tbody></table>

<table border="0" bgcolor="#e8e8e8" width="100%" style="margin:.2em 0;"><tbody><tr><td style="padding:.5em;"><pre style="margin:0;padding:0;">open -a Google\ Chrome --args --disable-web-security -–allow-file-access-from-files</pre></td></tr></tbody></table>

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;">You can find the source code of this example on <a href="https://github.com/kylehodgson/breeze-post-part-one">GitHub</a>.</td></tr></tbody></table>
