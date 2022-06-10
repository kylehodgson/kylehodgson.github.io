---
title: "KnockoutJS and Testing"
date: "2012-11-29"
categories: 
  - "agile"
  - "technology"
tags: 
  - "jasmine"
  - "javascript"
  - "knockoutjs"
  - "mvvm"
  - "phantomjs"
  - "tdd"
  - "tdd-knockout"
---

KnockoutJS is a JavaScript framework for making it easy to quickly create complex data based pages in browsers as old as IE6. It does this by though the use of a data-bind property for HTML elements like so:

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">&lt;input</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"value: firstName"</font> <b><font color="#0000FF">/&gt;</font></b></tt></pre></td></tr></tbody></table>

What the above data-bind will do is replace the value property of the input tag with whatever is in the firstName variable, and also track any changes the user makes to firstName and keep the model in sync. It’s a slick, declarative way to do things.

Knockout is an implementation of the [Model, View, ViewModel](http://en.wikipedia.org/wiki/Model_View_ViewModel) or MVVM pattern. This approach is thought to reduce client to server side coupling, provide easier testing of UI code, and make it simpler to get data where you want in the view.

In this blog post we’ll look at how you can test your KnockoutJS view model code using Jasmine and PhantomJS.

* * *

## Why not just use Selenium?

One of the great things about the MVVM pattern is that it lends itself to unit testing of the view model without having to test the view itself. Selenium does help you test your webpages and JavaScript, but it really specializes in automating the kinds of things a user would do with your page. You still need Selenium to automate this kind of testing. What this article seeks to do is teach you a technique to unit test your JavaScript - that is, test specific functions and make sure that each one of them is working individually - not just the page as a whole.

Also, Functional tests take a long time to run! Your code has to be installed on a running web server, Selenium has to start a browser, and that browser has to interact with your page one click at a time. With this technique, we’ll start a "headless" browser (which starts much more quickly) and simply read in a single HTML file that contains links to all our tests. These headless unit tests can be run in seconds.

* * *

## How Knockout Works

Below we’ll show a simple code sample - this one comes from the KnockoutJS Live Example on their website. While this doesn’t show the full power of KnockoutJS, it gives us a starting point.

The HTML is simple - a few inputs where the user can enter their first and last names, and then a span to put the computed value.

**HTML Source:**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt>  <b><font color="#0000FF">&lt;p&gt;</font></b>First name: <b><font color="#0000FF">&lt;input</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"value: firstName"</font> <b><font color="#0000FF">/&gt;&lt;/p&gt;</font></b>
  <b><font color="#0000FF">&lt;p&gt;</font></b>Last name: <b><font color="#0000FF">&lt;input</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"value: lastName"</font> <b><font color="#0000FF">/&gt;&lt;/p&gt;</font></b>
  <b><font color="#0000FF">&lt;h2&gt;</font></b>Hello, <b><font color="#0000FF">&lt;span</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"text: fullName"</font><b><font color="#0000FF">&gt;</font></b> <b><font color="#0000FF">&lt;/span&gt;</font></b>!<b><font color="#0000FF">&lt;/h2&gt;</font></b></tt></pre></td></tr></tbody></table>

Here’s the JavaScript. Our ViewModel serves a very simple purpose - just to check out what’s in firstName and lastName, then concatenate them with a space in between whenever they change.

**JavaScript:**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">var</font></b> PersonNameViewModel <font color="#990000">=</font> <b><font color="#0000FF">function</font></b><font color="#990000">(</font>first<font color="#990000">,</font> last<font color="#990000">)</font> <font color="#FF0000">{</font>
  <b><font color="#0000FF">var</font></b> self <font color="#990000">=</font> <b><font color="#0000FF">this</font></b><font color="#990000">;</font>
<div></div>
  self<font color="#990000">.</font>firstName <font color="#990000">=</font> ko<font color="#990000">.</font><b><font color="#000000">observable</font></b><font color="#990000">(</font>first<font color="#990000">);</font>
  self<font color="#990000">.</font>lastName <font color="#990000">=</font> ko<font color="#990000">.</font><b><font color="#000000">observable</font></b><font color="#990000">(</font>last<font color="#990000">);</font>
<div></div>
  self<font color="#990000">.</font>fullName <font color="#990000">=</font> ko<font color="#990000">.</font><b><font color="#000000">computed</font></b><font color="#990000">(</font><b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
    <b><font color="#0000FF">return</font></b> self<font color="#990000">.</font><b><font color="#000000">firstName</font></b><font color="#990000">()</font> <font color="#990000">+</font> <font color="#FF0000">" "</font> <font color="#990000">+</font> self<font color="#990000">.</font><b><font color="#000000">lastName</font></b><font color="#990000">();</font>
  <font color="#FF0000">}</font><font color="#990000">,</font> self<font color="#990000">);</font>
<font color="#FF0000">}</font><font color="#990000">;</font>
<div></div>
$<font color="#990000">(</font><b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
  ko<font color="#990000">.</font><b><font color="#000000">applyBindings</font></b><font color="#990000">(</font><b><font color="#0000FF">new</font></b> <b><font color="#000000">PersonNameViewModel</font></b><font color="#990000">(</font><font color="#FF0000">"Ada"</font><font color="#990000">,</font> <font color="#FF0000">"Lovelace"</font><font color="#990000">));</font>
<font color="#FF0000">}</font><font color="#990000">);</font>
</tt></pre></td></tr></tbody></table>

* * *

## Testing: KnockoutJS, Jasmine, and PhantomJS

Let’s get to the good part. My colleague [Peter](http://qingsongzhao.blogspot.ca/) showed me how to test KnockoutJS with [Jasmine](http://pivotal.github.com/jasmine/) and [PhantomJS](http://phantomjs.org/) - and I like the approach. Let’s try to use these tools to test our script.

**You’ll need the following:**

- **PhantomJS** you can find [on the PhantomJS site](http://phantomjs.org/download.html). Installing Phantom is simple, just download the zip file and add the bin directory and phantomjs to your path.
- **Jasmine** is found on the GitHub page [here](https://github.com/pivotal/jasmine/downloads). I downloaded jasmine-standalone-1.2.0.zip.
- The **Phantom-Jasmine** scripts you can find here [on the GitHub page](https://github.com/jcarver989/phantom-jasmine).

Once we have the tools, we can write a spec like this for our code:

**Hello World Spec**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#000000">describe</font></b><font color="#990000">(</font><font color="#FF0000">"Person Name"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
  <b><font color="#000000">it</font></b><font color="#990000">(</font><font color="#FF0000">"computes fullName based on firstName and lastName"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
    <b><font color="#0000FF">var</font></b> target <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">PersonNameViewModel</font></b><font color="#990000">(</font><font color="#FF0000">"Ada"</font><font color="#990000">,</font><font color="#FF0000">"Lovelace"</font><font color="#990000">);</font>
    <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">fullName</font></b><font color="#990000">()).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font><font color="#FF0000">"Ada Lovelace"</font><font color="#990000">);</font>
  <font color="#FF0000">}</font><font color="#990000">);</font>
<font color="#FF0000">}</font><font color="#990000">);</font></tt></pre></td></tr></tbody></table>

Then, we just include that spec in a test runner file. A minimal test runner looks like the one below.

**Hello World Jasmine Test Runner**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#000080">&lt;!DOCTYPE</font></b> <font color="#009900">HTML</font><b><font color="#000080">&gt;</font></b>
<b><font color="#0000FF">&lt;html&gt;</font></b>
<b><font color="#0000FF">&lt;head&gt;</font></b>
  <b><font color="#0000FF">&lt;title&gt;</font></b>Jasmine Test Runner<b><font color="#0000FF">&lt;/title&gt;</font></b>
  <b><font color="#0000FF">&lt;link</font></b> <font color="#009900">rel</font><font color="#990000">=</font><font color="#FF0000">"stylesheet"</font> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/css"</font> <font color="#009900">href</font><font color="#990000">=</font><font color="#FF0000">"jasmine-1.2.0/jasmine.css"</font> <b><font color="#0000FF">/&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"jasmine-1.2.0/jasmine.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"jasmine-1.2.0/jasmine-html.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"console-runner.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"http://ajax.aspnetcdn.com/ajax/knockout/knockout-2.1.0.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<div></div>

  <i><font color="#9A1900">&lt;!-- SOURCE FILES --&gt;</font></i>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"myscript.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<div></div>
  <i><font color="#9A1900">&lt;!-- TEST FILES --&gt;</font></i>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"myscript-spec.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<b><font color="#0000FF">&lt;/head&gt;</font></b>
<b><font color="#0000FF">&lt;body&gt;</font></b>
<div></div>
<b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><b><font color="#0000FF">&gt;</font></b>
  <b><font color="#0000FF">var</font></b> console_reporter <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> jasmine<font color="#990000">.</font><b><font color="#000000">ConsoleReporter</font></b><font color="#990000">()</font>
  jasmine<font color="#990000">.</font><b><font color="#000000">getEnv</font></b><font color="#990000">().</font><b><font color="#000000">addReporter</font></b><font color="#990000">(</font><b><font color="#0000FF">new</font></b> jasmine<font color="#990000">.</font><b><font color="#000000">TrivialReporter</font></b><font color="#990000">());</font>
  jasmine<font color="#990000">.</font><b><font color="#000000">getEnv</font></b><font color="#990000">().</font><b><font color="#000000">addReporter</font></b><font color="#990000">(</font>console_reporter<font color="#990000">);</font>
  jasmine<font color="#990000">.</font><b><font color="#000000">getEnv</font></b><font color="#990000">().</font><b><font color="#000000">execute</font></b><font color="#990000">();</font>
<b><font color="#0000FF">&lt;/script&gt;</font></b>
<div></div>
<b><font color="#0000FF">&lt;/body&gt;</font></b>
<b><font color="#0000FF">&lt;/html&gt;</font></b></tt></pre></td></tr></tbody></table>

Let’s look at that file one step at a time.

The first block is just pulling in the different JavaScript frameworks we need to run our code. It would actually be pretty easy to mock jQuery instead of using the real one. Also, note that it’s better to download the code for KnockoutJS and JQuery than using the CDN in this approach so that you can run your unit tests when you are offline.

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"</font><b><font color="#0000FF">&gt;</font></b> <b><font color="#0000FF">&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"jasmine-1.2.0/jasmine.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"jasmine-1.2.0/jasmine-html.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"console-runner.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"http://ajax.aspnetcdn.com/ajax/knockout/knockout-2.1.0.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b></tt></pre></td></tr></tbody></table>

The next code block imports your actual code that you want to test, and then your specs. Each time you add new ViewModels (usually one per page), you add a new spec line and a new source line to include the new files.

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt>  <i><font color="#9A1900">&lt;!-- SOURCE FILES --&gt;</font></i>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"myscript.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<div></div>
  <i><font color="#9A1900">&lt;!-- TEST FILES --&gt;</font></i>
  <b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"myscript-spec.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b></tt></pre></td></tr></tbody></table>

The third part is a bit of JavaScript that starts the test runner and reports the results.

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><b><font color="#0000FF">&gt;</font></b>
  <b><font color="#0000FF">var</font></b> console_reporter <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> jasmine<font color="#990000">.</font><b><font color="#000000">ConsoleReporter</font></b><font color="#990000">()</font>
  jasmine<font color="#990000">.</font><b><font color="#000000">getEnv</font></b><font color="#990000">().</font><b><font color="#000000">addReporter</font></b><font color="#990000">(</font><b><font color="#0000FF">new</font></b> jasmine<font color="#990000">.</font><b><font color="#000000">TrivialReporter</font></b><font color="#990000">());</font>
  jasmine<font color="#990000">.</font><b><font color="#000000">getEnv</font></b><font color="#990000">().</font><b><font color="#000000">addReporter</font></b><font color="#990000">(</font>console_reporter<font color="#990000">);</font>
  jasmine<font color="#990000">.</font><b><font color="#000000">getEnv</font></b><font color="#990000">().</font><b><font color="#000000">execute</font></b><font color="#990000">();</font>
<b><font color="#0000FF">&lt;/script&gt;</font></b></tt></pre></td></tr></tbody></table>

* * *

## Invocation

Once it’s all setup, you can just run phantomjs run\_jasmine\_test.coffee TestRunner.html in a terminal (or make a quick bash script to do it for you) and it will fire up PhantomJS and run your tests. You should see something like this if it worked:

<table border="0" bgcolor="#e8e8e8" width="100%" style="margin:.2em 0;"><tbody><tr><td style="padding:.5em;"><pre style="margin:0;padding:0;"> $ phantomjs run_jasmine_test.coffee TestRunner.html
 Starting...
<div></div>
 Finished
 -----------------
 1 spec, 0 failures in 0.013s.
<div></div>
 ConsoleReporter finished</pre></td></tr></tbody></table>

It’s really really fast!

* * *

## Wrap Up

So, as you can see, it is possible to not only run automated tests on JavaScript, but also you can actually write unit tests, not just functional or acceptance tests.

_By the way, I made this WordPress blog post with asciidoc and the blogpost.py script, which are awesome._
