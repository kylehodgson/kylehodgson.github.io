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

```javascript
var ToDontList = function (initialItems) {
    var self = this;

    if (!(initialItems instanceof Array))
        initialItems = [];
    self.items = ko.observableArray(initialItems);

    self.add_item = function (item) {
        self.items.push(item);
    };
};

describe("ToDontList View Model", function () {
    var test_item1 = { "title": "Test title",
      "description": "Test description", "complete": false };
    var test_item2 = { "title": "Another test title",
      "description": "Another test description", "complete": false };
    var test_items = [test_item1, test_item2];

    it("Should be able to add items", function() {
        var target = new ToDontList();
        target.add_item(test_item1);
        expect(target.items()[0].title).toBe(test_item1.title);
    });

    it("Should be able to view existing items", function () {
        var target = new ToDontList(test_items);
        expect(target.items().length).toBe(2);
        expect(target.items()[0].title).toBe(test_item1.title);
        expect(target.items()[1].title).toBe(test_item2.title);
    });
});
```

* * *

## Creating the UI

- Add a new item to the project (Ctrl-shift-a)
- Choose HTML page, name it index.html
- Add Knockout to the page:

**Adding Knockout to the bottom of the page**

```html
</body>
<script src="Scripts/knockout-2.2.1.js"></script>
</html>
```
- Add a list with a data-bind like this: <ul data-bind="foreach: items">
- Inside that <ul> tag add an <li> tag. We’ll place each "to Don’t" item here
- Inside the list item we’ll create divs for the title and the description

**Creating a list to hold data bound elements**

```html
<ul data-bind="foreach: items">
  <li>
    <div data-bind="text: title"></div>
    <div data-bind="text: description"></div>
  </li>
</ul>
```
- We’ll need to tell Knockout to boot, and inform it of what ViewModel we want to use.

**Booting Knockout**

```html
<script type="text/javascript">
  var viewModel = new ToDontList();
  ko.applyBindings(viewModel);
</script>
```
Our JavaScript code for the ViewModel is still in the test spec. Now that we need it in its own file, it’s a good time to do that.

* * *

## Creating the ViewModel file

At this stage, our tests still pass, but we’ve created a web page that doesn’t work. If we hit ctrl-F5 to run the page, and then ctrl-shift-I to pull up Chrome’s debugger (assuming you are running Chrome), you should see the following error in the console:

```
Uncaught ReferenceError: ToDontList is not defined
```

- Highlight the ViewModel function object from our specification in Visual Studio
- Cut the text of it with ctrl-x
- Save the changes to our specification - at this stage, the tests should fail (as ToDontList is no longer defined)
- Click the src folder in Visual Studio and use ctrl-shift-a to create a new file, choose JavaScript File as the type
- Name the file ToDontList.js
- Paste the code in to this file, save it - at this stage, the tests should pass again
- We’ll add the reference to this new file between the Knockout import and the <script> tag that boots knockout

At this stage we have a web view that should correctly import Knockout, import our ViewModel, boot knockout and apply any bindings it finds in the HTML to ViewModel elements. To simulate pulling data in from the server, we can borrow the array of test items from our specification like so:

**Wiring up test data for our ViewModel**

```html
</body>
<script src="Scripts/knockout-2.2.1.js"></script>
<script src="src/ToDontList.js"></script>
<script type="text/javascript">
    var test_item1 = { "title": "Test title",
      "description": "Test description", "complete": false };
    var test_item2 = { "title": "Another test title",
      "description": "Another test description", "complete": false };
    var test_items = [test_item1, test_item2];
    var viewModel = new ToDontList(test_items);
    ko.applyBindings(viewModel);
</script>
</html>
```
If we go to Chrome now and reload the page, we should see that our test items each show up now.

**Viewing the page**  
![images/tdd-knockout-11.png](images/tdd-knockout-11.png)

Here are the files we’ve created to date and their current state:

**spec/ToDontListSpec.js**

```javascript
describe("ToDontList View Model", function () {
    var test_item1 = { "title": "Test title",
      "description": "Test description", "complete": false };
    var test_item2 = { "title": "Another test title",
      "description": "Another test description", "complete": false };
    var test_items = [test_item1, test_item2];

    it("Should be able to add items", function() {
        var target = new ToDontList();
        target.add_item(test_item1);
        expect(target.items()[0].title).toBe(test_item1.title);
    });

    it("Should be able to view existing items", function () {
        var target = new ToDontList(test_items);
        expect(target.items().length).toBe(2);
        expect(target.items()[0].title).toBe(test_item1.title);
        expect(target.items()[1].title).toBe(test_item2.title);
    });
});
```

**src/ToDontList.js**

```javascript
var ToDontList = function (initialItems) {
    var self = this;

    if (!(initialItems instanceof Array))
        initialItems = [];
    self.items = ko.observableArray(initialItems);

    self.add_item = function (item) {
        self.items.push(item);
    };
};
```

**index.html**

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
</head>
<body>
    <div>
        <ul data-bind="foreach: items">
            <li>
                <div data-bind="text: title"></div>
                <div data-bind="text: description"></div>
            </li>
        </ul>
    </div>
</body>
<script src="Scripts/knockout-2.2.1.js"></script>
<script src="src/ToDontList.js"></script>
<script type="text/javascript">
    var test_item1 = { "title": "Test title",
      "description": "Test description", "complete": false };
    var test_item2 = { "title": "Another test title",
      "description": "Another test description", "complete": false };
    var test_items = [test_item1, test_item2];
    var viewModel = new ToDontList(test_items);
    ko.applyBindings(viewModel);
</script>
</html>
```

**karma.conf.js**

```javascript
// Karma configuration
// Generated on Thu May 30 2013 14:17:27 GMT-0400 (Eastern Daylight Time)


// base path, that will be used to resolve files and exclude
basePath = '';


// list of files / patterns to load in the browser
files = [
  JASMINE,
  JASMINE_ADAPTER,
  'Scripts/knockout-2.2.1.js',
  'src/**/*.js',
  'spec/**/*Spec*.js'
];


// list of files to exclude
exclude = [

];


// test results reporter to use
// possible values: 'dots', 'progress', 'junit'
reporters = ['progress'];


// web server port
port = 9876;


// cli runner port
runnerPort = 9100;


// enable / disable colors in the output (reporters and logs)
colors = true;


// level of logging
// possible values: LOG_DISABLE || LOG_ERROR || LOG_WARN || LOG_INFO || LOG_DEBUG
logLevel = LOG_INFO;


// enable / disable watching file and executing tests whenever any file changes
autoWatch = true;


// Start these browsers, currently available:
// - Chrome
// - ChromeCanary
// - Firefox
// - Opera
// - Safari (only Mac)
// - PhantomJS
// - IE (only Windows)
browsers = ['Chrome'];


// If browser does not capture in given timeout [ms], kill it
captureTimeout = 60000;


// Continuous Integration mode
// if true, it capture browsers, run tests and exit
singleRun = false;
```

* * *

## Progress so far

We now have a basic web view created that can view what items exist. In the next article, we’ll explore creating the web form to create new items and binding this form to the ViewModel’s add\_item method.

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;">This article is part of a multi-part series on Test Driven JavaScript development. The code for this particular project can be found on <a href="https://github.com/kylehodgson/ToDontList">GitHub</a>. You can view all the articles by viewing the <a href="http://kylehodgson.com/tag/tdd-knockout/">tdd-knockout</a> tag. This series also has a relevant <a href="https://github.com/kylehodgson/ToDontList">GitHub repository</a>.</td></tr></tbody></table>
