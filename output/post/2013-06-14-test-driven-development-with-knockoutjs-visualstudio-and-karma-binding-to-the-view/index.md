---
title: "Test Driven Development with KnockoutJS, VisualStudio, and Karma: Binding to the View"
date: "2013-06-14"
categories: 
  - "agile"
  - "technology"
tags: 
  - "javascript"
  - "karma-runner"
  - "knockoutjs"
  - "tdd"
  - "tdd-knockout"
---

In our [last post](http://kylehodgson.com/2013/05/29/test-driven-development-with-knockoutjs-visualstudio-and-karma/), we created a ViewModel which models the basic functionality we would like on the page. As a reminder, we have a Knockout "observable array" of items suitable for binding; each item is a JavaScript object containing a title and description in addition to a value called complete which indicates if this item has been completed. Our ViewModel function object accepts a parameter of any initial items we may need to load in to to our list from the server. We have a method called add\_item which can add new items to our list, and we have a test suite that ensures that all of this is working. So far, all of this is inside a single file called ToDontListSpec.js.

**Starting point**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">var</font></b> ToDontList <font color="#990000">=</font> <b><font color="#0000FF">function</font></b> <font color="#990000">(</font>initialItems<font color="#990000">)</font> <font color="#FF0000">{</font>
    <b><font color="#0000FF">var</font></b> self <font color="#990000">=</font> <b><font color="#0000FF">this</font></b><font color="#990000">;</font>
<div></div>
    <b><font color="#0000FF">if</font></b> <font color="#990000">(!(</font>initialItems <b><font color="#0000FF">instanceof</font></b> Array<font color="#990000">))</font>
        initialItems <font color="#990000">=</font> <font color="#990000">[];</font>
    self<font color="#990000">.</font>items <font color="#990000">=</font> ko<font color="#990000">.</font><b><font color="#000000">observableArray</font></b><font color="#990000">(</font>initialItems<font color="#990000">);</font>
<div></div>
    self<font color="#990000">.</font>add_item <font color="#990000">=</font> <b><font color="#0000FF">function</font></b> <font color="#990000">(</font>item<font color="#990000">)</font> <font color="#FF0000">{</font>
        self<font color="#990000">.</font>items<font color="#990000">.</font><b><font color="#000000">push</font></b><font color="#990000">(</font>item<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">;</font>
<font color="#FF0000">}</font><font color="#990000">;</font>
<div></div>
<b><font color="#000000">describe</font></b><font color="#990000">(</font><font color="#FF0000">"ToDontList View Model"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b> <font color="#990000">()</font> <font color="#FF0000">{</font>
    <b><font color="#0000FF">var</font></b> test_item1 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_item2 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Another test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Another test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_items <font color="#990000">=</font> <font color="#990000">[</font>test_item1<font color="#990000">,</font> test_item2<font color="#990000">];</font>
<div></div>
    <b><font color="#000000">it</font></b><font color="#990000">(</font><font color="#FF0000">"Should be able to add items"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
        <b><font color="#0000FF">var</font></b> target <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">();</font>
        target<font color="#990000">.</font><b><font color="#000000">add_item</font></b><font color="#990000">(</font>test_item1<font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">()[</font><font color="#993399">0</font><font color="#990000">].</font>title<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font>test_item1<font color="#990000">.</font>title<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">);</font>
<div></div>
    <b><font color="#000000">it</font></b><font color="#990000">(</font><font color="#FF0000">"Should be able to view existing items"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b> <font color="#990000">()</font> <font color="#FF0000">{</font>
        <b><font color="#0000FF">var</font></b> target <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">(</font>test_items<font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">().</font>length<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font><font color="#993399">2</font><font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">()[</font><font color="#993399">0</font><font color="#990000">].</font>title<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font>test_item1<font color="#990000">.</font>title<font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">()[</font><font color="#993399">1</font><font color="#990000">].</font>title<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font>test_item2<font color="#990000">.</font>title<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">);</font>
<font color="#FF0000">}</font><font color="#990000">);</font></tt></pre></td></tr></tbody></table>

* * *

## Creating the UI

- Add a new item to the project (Ctrl-shift-a)
- Choose HTML page, name it index.html
- Add Knockout to the page:

**Adding Knockout to the bottom of the page**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><font color="#990000">&lt;/</font>body<font color="#990000">&gt;</font>
<font color="#990000">&lt;</font>script src<font color="#990000">=</font><font color="#FF0000">"Scripts/knockout-2.2.1.js"</font><font color="#990000">&gt;&lt;/</font>script<font color="#990000">&gt;</font>
<font color="#990000">&lt;/</font>html<font color="#990000">&gt;</font></tt></pre></td></tr></tbody></table>

- Add a list with a data-bind like this: <ul data-bind="foreach: items">
- Inside that <ul> tag add an <li> tag. We’ll place each "to Don’t" item here
- Inside the list item we’ll create divs for the title and the description

**Creating a list to hold data bound elements**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><font color="#990000">&lt;</font>ul data<font color="#990000">-</font>bind<font color="#990000">=</font><font color="#FF0000">"foreach: items"</font><font color="#990000">&gt;</font>
  <font color="#990000">&lt;</font>li<font color="#990000">&gt;</font>
    <font color="#990000">&lt;</font>div data<font color="#990000">-</font>bind<font color="#990000">=</font><font color="#FF0000">"text: title"</font><font color="#990000">&gt;&lt;/</font>div<font color="#990000">&gt;</font>
    <font color="#990000">&lt;</font>div data<font color="#990000">-</font>bind<font color="#990000">=</font><font color="#FF0000">"text: description"</font><font color="#990000">&gt;&lt;/</font>div<font color="#990000">&gt;</font>
  <font color="#990000">&lt;/</font>li<font color="#990000">&gt;</font>
<font color="#990000">&lt;/</font>ul<font color="#990000">&gt;</font></tt></pre></td></tr></tbody></table>

- We’ll need to tell Knockout to boot, and inform it of what ViewModel we want to use.

**Booting Knockout**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><font color="#990000">&lt;</font>script type<font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><font color="#990000">&gt;</font>
  <b><font color="#0000FF">var</font></b> viewModel <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">();</font>
  ko<font color="#990000">.</font><b><font color="#000000">applyBindings</font></b><font color="#990000">(</font>viewModel<font color="#990000">);</font>
<font color="#990000">&lt;/</font>script<font color="#990000">&gt;</font></tt></pre></td></tr></tbody></table>

Our JavaScript code for the ViewModel is still in the test spec. Now that we need it in its own file, it’s a good time to do that.

* * *

## Creating the ViewModel file

At this stage, our tests still pass, but we’ve created a web page that doesn’t work. If we hit ctrl-F5 to run the page, and then ctrl-shift-I to pull up Chrome’s debugger (assuming you are running Chrome), you should see the following error in the console:

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt>Uncaught ReferenceError<font color="#990000">:</font> ToDontList is not defined</tt></pre></td></tr></tbody></table>

- Highlight the ViewModel function object from our specification in Visual Studio
- Cut the text of it with ctrl-x
- Save the changes to our specification - at this stage, the tests should fail (as ToDontList is no longer defined)
- Click the src folder in Visual Studio and use ctrl-shift-a to create a new file, choose JavaScript File as the type
- Name the file ToDontList.js
- Paste the code in to this file, save it - at this stage, the tests should pass again
- We’ll add the reference to this new file between the Knockout import and the <script> tag that boots knockout

At this stage we have a web view that should correctly import Knockout, import our ViewModel, boot knockout and apply any bindings it finds in the HTML to ViewModel elements. To simulate pulling data in from the server, we can borrow the array of test items from our specification like so:

**Wiring up test data for our ViewModel**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><font color="#990000">&lt;/</font>body<font color="#990000">&gt;</font>
<font color="#990000">&lt;</font>script src<font color="#990000">=</font><font color="#FF0000">"Scripts/knockout-2.2.1.js"</font><font color="#990000">&gt;&lt;/</font>script<font color="#990000">&gt;</font>
<font color="#990000">&lt;</font>script src<font color="#990000">=</font><font color="#FF0000">"src/ToDontList.js"</font><font color="#990000">&gt;&lt;/</font>script<font color="#990000">&gt;</font>
<font color="#990000">&lt;</font>script type<font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><font color="#990000">&gt;</font>
    <b><font color="#0000FF">var</font></b> test_item1 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_item2 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Another test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Another test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_items <font color="#990000">=</font> <font color="#990000">[</font>test_item1<font color="#990000">,</font> test_item2<font color="#990000">];</font>
    <b><font color="#0000FF">var</font></b> viewModel <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">(</font>test_items<font color="#990000">);</font>
    ko<font color="#990000">.</font><b><font color="#000000">applyBindings</font></b><font color="#990000">(</font>viewModel<font color="#990000">);</font>
<font color="#990000">&lt;/</font>script<font color="#990000">&gt;</font>
<font color="#990000">&lt;/</font>html<font color="#990000">&gt;</font></tt></pre></td></tr></tbody></table>

If we go to Chrome now and reload the page, we should see that our test items each show up now.

**Viewing the page**  
![images/tdd-knockout-11.png](images/tdd-knockout-11.png)

Here are the files we’ve created to date and their current state:

**spec/ToDontListSpec.js**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#000000">describe</font></b><font color="#990000">(</font><font color="#FF0000">"ToDontList View Model"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b> <font color="#990000">()</font> <font color="#FF0000">{</font>
    <b><font color="#0000FF">var</font></b> test_item1 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_item2 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Another test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Another test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_items <font color="#990000">=</font> <font color="#990000">[</font>test_item1<font color="#990000">,</font> test_item2<font color="#990000">];</font>
<div></div>
    <b><font color="#000000">it</font></b><font color="#990000">(</font><font color="#FF0000">"Should be able to add items"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b><font color="#990000">()</font> <font color="#FF0000">{</font>
        <b><font color="#0000FF">var</font></b> target <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">();</font>
        target<font color="#990000">.</font><b><font color="#000000">add_item</font></b><font color="#990000">(</font>test_item1<font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">()[</font><font color="#993399">0</font><font color="#990000">].</font>title<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font>test_item1<font color="#990000">.</font>title<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">);</font>
<div></div>
    <b><font color="#000000">it</font></b><font color="#990000">(</font><font color="#FF0000">"Should be able to view existing items"</font><font color="#990000">,</font> <b><font color="#0000FF">function</font></b> <font color="#990000">()</font> <font color="#FF0000">{</font>
        <b><font color="#0000FF">var</font></b> target <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">(</font>test_items<font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">().</font>length<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font><font color="#993399">2</font><font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">()[</font><font color="#993399">0</font><font color="#990000">].</font>title<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font>test_item1<font color="#990000">.</font>title<font color="#990000">);</font>
        <b><font color="#000000">expect</font></b><font color="#990000">(</font>target<font color="#990000">.</font><b><font color="#000000">items</font></b><font color="#990000">()[</font><font color="#993399">1</font><font color="#990000">].</font>title<font color="#990000">).</font><b><font color="#000000">toBe</font></b><font color="#990000">(</font>test_item2<font color="#990000">.</font>title<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">);</font>
<font color="#FF0000">}</font><font color="#990000">);</font></tt></pre></td></tr></tbody></table>

**src/ToDontList.js**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">var</font></b> ToDontList <font color="#990000">=</font> <b><font color="#0000FF">function</font></b> <font color="#990000">(</font>initialItems<font color="#990000">)</font> <font color="#FF0000">{</font>
    <b><font color="#0000FF">var</font></b> self <font color="#990000">=</font> <b><font color="#0000FF">this</font></b><font color="#990000">;</font>
<div></div>
    <b><font color="#0000FF">if</font></b> <font color="#990000">(!(</font>initialItems <b><font color="#0000FF">instanceof</font></b> Array<font color="#990000">))</font>
        initialItems <font color="#990000">=</font> <font color="#990000">[];</font>
    self<font color="#990000">.</font>items <font color="#990000">=</font> ko<font color="#990000">.</font><b><font color="#000000">observableArray</font></b><font color="#990000">(</font>initialItems<font color="#990000">);</font>
<div></div>
    self<font color="#990000">.</font>add_item <font color="#990000">=</font> <b><font color="#0000FF">function</font></b> <font color="#990000">(</font>item<font color="#990000">)</font> <font color="#FF0000">{</font>
        self<font color="#990000">.</font>items<font color="#990000">.</font><b><font color="#000000">push</font></b><font color="#990000">(</font>item<font color="#990000">);</font>
    <font color="#FF0000">}</font><font color="#990000">;</font>
<font color="#FF0000">}</font><font color="#990000">;</font></tt></pre></td></tr></tbody></table>

**index.html**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#000080">&lt;!DOCTYPE</font></b> <font color="#009900">html</font><b><font color="#000080">&gt;</font></b>
<b><font color="#0000FF">&lt;html</font></b> <font color="#009900">xmlns</font><font color="#990000">=</font><font color="#FF0000">"http://www.w3.org/1999/xhtml"</font><b><font color="#0000FF">&gt;</font></b>
<b><font color="#0000FF">&lt;head&gt;</font></b>
    <b><font color="#0000FF">&lt;title&gt;&lt;/title&gt;</font></b>
<b><font color="#0000FF">&lt;/head&gt;</font></b>
<b><font color="#0000FF">&lt;body&gt;</font></b>
    <b><font color="#0000FF">&lt;div&gt;</font></b>
        <b><font color="#0000FF">&lt;ul</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"foreach: items"</font><b><font color="#0000FF">&gt;</font></b>
            <b><font color="#0000FF">&lt;li&gt;</font></b>
                <b><font color="#0000FF">&lt;div</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"text: title"</font><b><font color="#0000FF">&gt;&lt;/div&gt;</font></b>
                <b><font color="#0000FF">&lt;div</font></b> <font color="#009900">data-bind</font><font color="#990000">=</font><font color="#FF0000">"text: description"</font><b><font color="#0000FF">&gt;&lt;/div&gt;</font></b>
            <b><font color="#0000FF">&lt;/li&gt;</font></b>
        <b><font color="#0000FF">&lt;/ul&gt;</font></b>
    <b><font color="#0000FF">&lt;/div&gt;</font></b>
<b><font color="#0000FF">&lt;/body&gt;</font></b>
<b><font color="#0000FF">&lt;script</font></b> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"Scripts/knockout-2.2.1.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<b><font color="#0000FF">&lt;script</font></b> <font color="#009900">src</font><font color="#990000">=</font><font color="#FF0000">"src/ToDontList.js"</font><b><font color="#0000FF">&gt;&lt;/script&gt;</font></b>
<b><font color="#0000FF">&lt;script</font></b> <font color="#009900">type</font><font color="#990000">=</font><font color="#FF0000">"text/javascript"</font><b><font color="#0000FF">&gt;</font></b>
    <b><font color="#0000FF">var</font></b> test_item1 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_item2 <font color="#990000">=</font> <font color="#FF0000">{</font> <font color="#FF0000">"title"</font><font color="#990000">:</font> <font color="#FF0000">"Another test title"</font><font color="#990000">,</font>
      <font color="#FF0000">"description"</font><font color="#990000">:</font> <font color="#FF0000">"Another test description"</font><font color="#990000">,</font> <font color="#FF0000">"complete"</font><font color="#990000">:</font> <b><font color="#0000FF">false</font></b> <font color="#FF0000">}</font><font color="#990000">;</font>
    <b><font color="#0000FF">var</font></b> test_items <font color="#990000">=</font> <font color="#990000">[</font>test_item1<font color="#990000">,</font> test_item2<font color="#990000">];</font>
    <b><font color="#0000FF">var</font></b> viewModel <font color="#990000">=</font> <b><font color="#0000FF">new</font></b> <b><font color="#000000">ToDontList</font></b><font color="#990000">(</font>test_items<font color="#990000">);</font>
    ko<font color="#990000">.</font><b><font color="#000000">applyBindings</font></b><font color="#990000">(</font>viewModel<font color="#990000">);</font>
<b><font color="#0000FF">&lt;/script&gt;</font></b>
<b><font color="#0000FF">&lt;/html&gt;</font></b></tt></pre></td></tr></tbody></table>

**karma.conf.js**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><i><font color="#9A1900">// Karma configuration</font></i>
<i><font color="#9A1900">// Generated on Thu May 30 2013 14:17:27 GMT-0400 (Eastern Daylight Time)</font></i>
<div></div>

<i><font color="#9A1900">// base path, that will be used to resolve files and exclude</font></i>
basePath <font color="#990000">=</font> <font color="#FF0000">''</font><font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// list of files / patterns to load in the browser</font></i>
files <font color="#990000">=</font> <font color="#990000">[</font>
  JASMINE<font color="#990000">,</font>
  JASMINE_ADAPTER<font color="#990000">,</font>
  <font color="#FF0000">'Scripts/knockout-2.2.1.js'</font><font color="#990000">,</font>
  <font color="#FF0000">'src/**/*.js'</font><font color="#990000">,</font>
  <font color="#FF0000">'spec/**/*Spec*.js'</font>
<font color="#990000">];</font>
<div></div>

<i><font color="#9A1900">// list of files to exclude</font></i>
exclude <font color="#990000">=</font> <font color="#990000">[</font>
<div></div>
<font color="#990000">];</font>
<div></div>

<i><font color="#9A1900">// test results reporter to use</font></i>
<i><font color="#9A1900">// possible values: 'dots', 'progress', 'junit'</font></i>
reporters <font color="#990000">=</font> <font color="#990000">[</font><font color="#FF0000">'progress'</font><font color="#990000">];</font>
<div></div>

<i><font color="#9A1900">// web server port</font></i>
port <font color="#990000">=</font> <font color="#993399">9876</font><font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// cli runner port</font></i>
runnerPort <font color="#990000">=</font> <font color="#993399">9100</font><font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// enable / disable colors in the output (reporters and logs)</font></i>
colors <font color="#990000">=</font> <b><font color="#0000FF">true</font></b><font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// level of logging</font></i>
<i><font color="#9A1900">// possible values: LOG_DISABLE || LOG_ERROR || LOG_WARN || LOG_INFO || LOG_DEBUG</font></i>
logLevel <font color="#990000">=</font> LOG_INFO<font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// enable / disable watching file and executing tests whenever any file changes</font></i>
autoWatch <font color="#990000">=</font> <b><font color="#0000FF">true</font></b><font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// Start these browsers, currently available:</font></i>
<i><font color="#9A1900">// - Chrome</font></i>
<i><font color="#9A1900">// - ChromeCanary</font></i>
<i><font color="#9A1900">// - Firefox</font></i>
<i><font color="#9A1900">// - Opera</font></i>
<i><font color="#9A1900">// - Safari (only Mac)</font></i>
<i><font color="#9A1900">// - PhantomJS</font></i>
<i><font color="#9A1900">// - IE (only Windows)</font></i>
browsers <font color="#990000">=</font> <font color="#990000">[</font><font color="#FF0000">'Chrome'</font><font color="#990000">];</font>
<div></div>

<i><font color="#9A1900">// If browser does not capture in given timeout [ms], kill it</font></i>
captureTimeout <font color="#990000">=</font> <font color="#993399">60000</font><font color="#990000">;</font>
<div></div>

<i><font color="#9A1900">// Continuous Integration mode</font></i>
<i><font color="#9A1900">// if true, it capture browsers, run tests and exit</font></i>
singleRun <font color="#990000">=</font> <b><font color="#0000FF">false</font></b><font color="#990000">;</font></tt></pre></td></tr></tbody></table>

* * *

## Progress so far

We now have a basic web view created that can view what items exist. In the next article, we’ll explore creating the web form to create new items and binding this form to the ViewModel’s add\_item method.

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;">This article is part of a multi-part series on Test Driven JavaScript development. The code for this particular project can be found on <a href="https://github.com/kylehodgson/ToDontList">GitHub</a>. You can view all the articles by viewing the <a href="http://kylehodgson.com/tag/tdd-knockout/">tdd-knockout</a> tag. This series also has a relevant <a href="https://github.com/kylehodgson/ToDontList">GitHub repository</a>.</td></tr></tbody></table>
