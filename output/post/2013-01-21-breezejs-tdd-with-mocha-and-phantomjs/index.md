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

```javascript
var query = new breeze.EntityQuery()
  .from("Employees")
  .where("LastName", "startsWith", "P")
  .orderBy("LastName");
```


I’m attracted to the fluent aproach; and to the idea that you can build up queries client side that interact intelligently with a back end. Under the covers, this filter is being passed to a remote facade that can interpret the query - we aren’t downloading all of /api/Employees/ to do this. Breeze achieves this by interacting with remote [OData](http://en.wikipedia.org/wiki/Open_Data_Protocol) [endpoints](http://www.odata.org/libraries).

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><p><b>OData</b></p>According to Wikipedia, the Open Data Protocol (a.k.a OData) is a data access protocol from Microsoft. It is similar to JDBC and ODBC although OData is not limited to SQL databases. OData is built on the Atom Publishing Protocol and JSON where the Atom structure is the envelope that contains the data returned from each OData request. An OData request uses the REST model for all requests.</td></tr></tbody></table>

I’d like to explore using BreezeJS with a JVM language. There is a Java library for producing and consuming OData called [odata4j](http://code.google.com/p/odata4j/) which I’m excited about. It’s my practice to start with a failing test - so step one is to get Breeze in a working test harness. If this goes well, we’ll keep going and try to connect it with odata4j. Though the [Breeze project uses qunit](http://www.breezejs.com/documentation/testing-breeze-application-0), I’ve heard good things about Mocha. Early tests indicate that BreezeJS references window, so I’ll need something like [mocha-phantomjs](https://github.com/metaskills/mocha-phantomjs). The mocha-phantomjs GitHub readme talks about using a project called [ChaiJS](http://chaijs.com/) for assertions, so I’ll pull that in as well. The steps I used to get mocha up and running with breeze are below. While I don’t intend to use NodeJS in this tutorial, it seems like the quickest way to get Mocha going is to use Node’s npm package manager.

**Installing Mocha And Breeze Dependencies**

```
brew install phantomjs
brew install node
mkdir lib; cd lib
npm install mocha
npm install chai
npm install q
npm install mocha-phantomjs
```

Breeze can’t be installed via npm, of course. The BreezeJS project has put a lot of effort in to making sure that the nuget path works smoothly, but I don’t have nuget on my Mac. There is a zip file to download, however, if you check their [download page](http://www.breezejs.com/). I downloaded [runtime 0.85.2](http://www.breezejs.com/sites/all/packages/breeze-runtime-0.85.2.zip), and unzipped it.

**Installing BreezeJS**

```
mkdir breezejs
cd breezejs
wget http://www.breezejs.com/sites/all/packages/breeze-runtime-0.85.2.zip
unzip breeze-runtime-0.85.2.zip
rm -Rf WebApi/
```

## Assembling the Test Harness

Let’s start with a test runner and a bare bones test that proves that we can access Breeze. Here’s my working test-runner:

**Test Runner _test/JavaScript/test-runner.html_**

```html
<html>
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="../../lib/node_modules/mocha/mocha.css" />
  </head>
  <body>
    <div id="mocha"></div>

    <!-- infrastructure and dependencies -->
    <script type="text/javascript"
      src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js" ></script>
    <script type="text/javascript" src="../../lib/node_modules/mocha/mocha.js" ></script>
    <script type="text/javascript" src="../../lib/node_modules/chai/chai.js" ></script>
    <script type="text/javascript" src="../../lib/node_modules/q/q.js" ></script>
    <script type="text/javascript" src="../../lib/breezejs/Scripts/breeze.js" ></script>

    <!-- setting up mocha and chai -->
    <script type="text/javascript">
      mocha.ui('bdd');
      mocha.reporter('html');
      expect = chai.expect;
      assert = chai.assert;
    </script>

    <!-- include our specifications -->
    <script type="text/javascript" src="breeze-dependencies.js"></script>
    <script type="text/javascript" src="TestNetflixClient.js"></script>

    <!-- run the tests -->
    <script type="text/javascript">
      if (window.mochaPhantomJS) { mochaPhantomJS.run(); }
      else { mocha.run(); }
    </script>
  </body>
</html>
```

**Specification _test/JavaScript/breeze-dependencies.js_**

```javascript
describe('NetflixClient', function() {
    it('should be able to use BreezeJS', function() {
            var manager = new breeze.EntityManager({
                    serviceName: "http://odata.netflix.com/v2/Catalog/"
                });
            var query = breeze.EntityQuery.from("Products");
            return manager.executeQuery(query);
    })
});
```

An astute reader will notice that this test doesn’t do much, it just makes sure that we have Breeze in a harness. Now, we can run this test on the command line like so to see if our tests pass.

```
$ mocha-phantomjs test/JavaScript/test-runner.html


  NetflixClient
    ✓ should be able to use BreezeJS


  1 test complete (18 ms)
```

Excellent! We have a workable test environment.

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Tip</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><p><b>Debugging</b></p>To get the test-runner set up how I wanted, I often opened it in Chrome so that I could read the console to help troubleshoot issues. This worked fine with one exception: BreezeJS is trying to make XmlHttpRequests back to a live OData endpoint, but you’ve loaded the page from a file:// URL. In this case, your origin is null, and Chrome’s CORS rules prevent you from accessing the endpoint. While it would be a really bad idea to do this all the time, when you need debugging you can start Chrome with this command to alleviate this issue:</td></tr></tbody></table>

```
open -a Google\ Chrome --args --disable-web-security -–allow-file-access-from-files
```

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;">You can find the source code of this example on <a href="https://github.com/kylehodgson/breeze-post-part-one">GitHub</a>.</td></tr></tbody></table>
