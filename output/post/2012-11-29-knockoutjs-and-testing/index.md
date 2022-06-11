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

```html
<input data-bind="value: firstName" />
```

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

```html
  <p>First name: <input data-bind="value: firstName" /></p>
  <p>Last name: <input data-bind="value: lastName" /></p>
  <h2>Hello, <span data-bind="text: fullName"> </span>!</h2>
```

Here’s the JavaScript. Our ViewModel serves a very simple purpose - just to check out what’s in firstName and lastName, then concatenate them with a space in between whenever they change.

**JavaScript:**

```javascript
var PersonNameViewModel = function(first, last) {
  var self = this;

  self.firstName = ko.observable(first);
  self.lastName = ko.observable(last);

  self.fullName = ko.computed(function() {
    return self.firstName() + " " + self.lastName();
  }, self);
};

$(function() {
  ko.applyBindings(new PersonNameViewModel("Ada", "Lovelace"));
});
```

* * *

## Testing: KnockoutJS, Jasmine, and PhantomJS

Let’s get to the good part. My colleague [Peter](http://qingsongzhao.blogspot.ca/) showed me how to test KnockoutJS with [Jasmine](http://pivotal.github.com/jasmine/) and [PhantomJS](http://phantomjs.org/) - and I like the approach. Let’s try to use these tools to test our script.

**You’ll need the following:**

- **PhantomJS** you can find [on the PhantomJS site](http://phantomjs.org/download.html). Installing Phantom is simple, just download the zip file and add the bin directory and phantomjs to your path.
- **Jasmine** is found on the GitHub page [here](https://github.com/pivotal/jasmine/downloads). I downloaded jasmine-standalone-1.2.0.zip.
- The **Phantom-Jasmine** scripts you can find here [on the GitHub page](https://github.com/jcarver989/phantom-jasmine).

Once we have the tools, we can write a spec like this for our code:

**Hello World Spec**

```javascript
describe("Person Name", function() {
  it("computes fullName based on firstName and lastName", function() {
    var target = new PersonNameViewModel("Ada","Lovelace");
    expect(target.fullName()).toBe("Ada Lovelace");
  });
});
```

Then, we just include that spec in a test runner file. A minimal test runner looks like the one below.

**Hello World Jasmine Test Runner**

```html
<!DOCTYPE HTML>
<html>
<head>
  <title>Jasmine Test Runner</title>
  <link rel="stylesheet" type="text/css" href="jasmine-1.2.0/jasmine.css" />
  <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
  <script type="text/javascript" src="jasmine-1.2.0/jasmine.js"></script>
  <script type="text/javascript" src="jasmine-1.2.0/jasmine-html.js"></script>
  <script type="text/javascript" src="console-runner.js"></script>
  <script type="text/javascript" src="http://ajax.aspnetcdn.com/ajax/knockout/knockout-2.1.0.js"></script>


  <!-- SOURCE FILES -->
  <script type="text/javascript" src="myscript.js"></script>

  <!-- TEST FILES -->
  <script type="text/javascript" src="myscript-spec.js"></script>
</head>
<body>

<script type="text/javascript">
  var console_reporter = new jasmine.ConsoleReporter()
  jasmine.getEnv().addReporter(new jasmine.TrivialReporter());
  jasmine.getEnv().addReporter(console_reporter);
  jasmine.getEnv().execute();
</script>

</body>
</html>
```

Let’s look at that file one step at a time.

The first block is just pulling in the different JavaScript frameworks we need to run our code. It would actually be pretty easy to mock jQuery instead of using the real one. Also, note that it’s better to download the code for KnockoutJS and JQuery than using the CDN in this approach so that you can run your unit tests when you are offline.

```html
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"> </script>
  <script type="text/javascript" src="jasmine-1.2.0/jasmine.js"></script>
  <script type="text/javascript" src="jasmine-1.2.0/jasmine-html.js"></script>
  <script type="text/javascript" src="console-runner.js"></script>
  <script type="text/javascript" src="http://ajax.aspnetcdn.com/ajax/knockout/knockout-2.1.0.js"></script>
```

The next code block imports your actual code that you want to test, and then your specs. Each time you add new ViewModels (usually one per page), you add a new spec line and a new source line to include the new files.

```html
 <!-- SOURCE FILES -->
  <script type="text/javascript" src="myscript.js"></script>

  <!-- TEST FILES -->
  <script type="text/javascript" src="myscript-spec.js"></script>
  ```

The third part is a bit of JavaScript that starts the test runner and reports the results.

```html
<script type="text/javascript">
  var console_reporter = new jasmine.ConsoleReporter()
  jasmine.getEnv().addReporter(new jasmine.TrivialReporter());
  jasmine.getEnv().addReporter(console_reporter);
  jasmine.getEnv().execute();
</script>
```

* * *

## Invocation

Once it’s all setup, you can just run phantomjs run\_jasmine\_test.coffee TestRunner.html in a terminal (or make a quick bash script to do it for you) and it will fire up PhantomJS and run your tests. You should see something like this if it worked:

```
 $ phantomjs run_jasmine_test.coffee TestRunner.html
 Starting...

 Finished
 -----------------
 1 spec, 0 failures in 0.013s.

 ConsoleReporter finished
 ```

It’s really really fast!

* * *

## Wrap Up

So, as you can see, it is possible to not only run automated tests on JavaScript, but also you can actually write unit tests, not just functional or acceptance tests.

_By the way, I made this WordPress blog post with asciidoc and the blogpost.py script, which are awesome._
