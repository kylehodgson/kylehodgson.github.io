---
title: "Seven Reasons to Love Duck Angular"
date: "2014-04-30"
categories: 
  - "agile"
  - "technology"
tags: 
  - "angularjs"
  - "duck-angular"
  - "javascript"
  - "open-source"
  - "testing"
---

## Duck Angular

A team I'm working with saw a gap when they were testing AngularJS apps:

- Functional regression, while necessary, didn't provide fast enough feedback.
- Using unit testing techniques they could obviously test the JavaScript functions behind their Angular controllers, but that didn't help them assert that bindings were correct or that directives actually manipulated the DOM correctly.
- Fixtures worked, but had their own problems, and quickly became very complicated to set up.

What's a dev to do?

Enter [Duck Angular](https://github.com/asengupta/duck-angular): a framework specifically designed to help JavaScript developers test DOM interactions when working with [AngularJS](https://angularjs.org/) in a nearly immediate feedback loop at dev time.

## Seven Reasons to Love The Duck

- It runs insanely quickly inside of KarmaJS, allowing development time fast feedback on JavaScript changes. The team's test suite runs ~ 600 tests in around 50 seconds (and could be faster with some easy optimization)
- Binds your apps HTML templates (or just HTML files) with your controller and renders the result
- Duck is barely any code at all (293 lines), as it leverages Angular's internals to provide a full-fledged DOM in your tests so that you can make assertions about DOM interaction
- It's open source and available on GitHub at https://github.com/asengupta/duck-angular
- Works with Jamsine, mocha, Karma, phantomJS, RequireJS, chai, many of the "as promised" libraries, and much more.
- Allows you to flexibly inject your own dependencies, mocked or otherwise
- You can simulate user interactions with a handy helper function interactWith that can specify an element to interact with and a specific interaction

## Anatomy of a Duck Spec

Let's jump in and look at an example Duck Angular spec.

```
it("can test your bindings", function() {
    var container = new Container(injector, null, {baseUrl: "/base/src", textPluginPath: "lib/text"});
    var done = false;
    runs(function() {
      container.mvc("ListController", "index.html").then(function(mvc) {
        var dom = new DuckDOM(mvc.view, mvc.scope);
        var listItems = dom.element("ul#list &gt; li");
        expect(listItems.length).toBe(2);
        done = true;
      });
    });
    waitsFor(function() {
      return done;
    });
  });
```

## Breaking it Down

Let's start at the beginning. First, the line `var container = new Container();` creates a new Duck container. The container knows how to bind view templates with controllers and passes a full fledged DOM in to a promise:

```
container.mvc("ListController", "index.html").then(function(mvc) {} ) 
```

Inside the promise, we can create a full-fledged DOM in one line:

```
var dom = new DuckDOM(mvc.view, mvc.scope);
```

With our trusty new DOM, we can start making assertions as you'd expect, using selectors to locate DOM elements and making assertions on the values found there.

```
var listItems = dom.element("ul#list &gt; li");
expect(listItems.length).toBe(2);
```

We can simulate user interaction with `interactWith()` - just pass in an HTML element and it will click it, or select a drop down element, or whatever is required. By default it will click an element by simply passing in the element like this:

```
var addButton = dom.element("#addItem");
dom.interactWith(addButton);
```

## Learning More

Check out the author's [blog series](http://avishek.net/blog/?p=1188) for a great guide on using Duck Angular for more.
