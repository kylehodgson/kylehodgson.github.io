---
title: "ServiceStack 4 HTTP Utilities: Functional Contract Testing"
date: "2014-06-04"
categories: 
  - "agile"
  - "technology"
tags: 
  - "c"
  - "contract-testing"
  - "service-stack"
---

## ServiceStack 4 HTTP Utilities: Functional Contract Testing

ServiceStack 4 comes with some really great new tools for accessing an HTTP resource using a fluent syntax. They make a great addition - sometimes the JsonServiceClient (often just called the C# client) is a little too smart! For instance, let’s say you’ve published a specification saying how your service will respond to specific requests. Perhaps your spec says that you’ll always return a list of cities when a customer hits a URL `/cities` with a GET request. The trouble is, if you make a typo or change your `Route()` annotation without thinking about all the clients out there that still use the old endpoint, the JsonServiceClient will happily pick up on the new routes and not let you know that you’ve invalidated your contract. This is where Functional Contract Testing comes in.

> Want more on ServiceStack? [ServiceStack 4 Cookbook](http://kylehodgson.com/servicestack-cookbook/) contains over 70 recipes on building quality services and applications with ServiceStack!

[While Integration Contract testing](http://martinfowler.com/bliki/IntegrationContractTest.html) has a lot in common with functional testing and integration testing, this is something slightly different. While integration tests assert that different layers of an application are working together, Contract Functional tests prove that your publicly available API still meets a baseline of expected functionality. If your service has a single consumer, and your team is in charge of it, you may not need these techniques – however, if your team wants to make sure that the service you’re writing continues to fit a given specification (for instance API documentation that you’ve published), this technique may be perfect for you.

Let’s take a look at how the HTTP Utilities can help.

## Using HTTP Utilities

Let’s say we had a service PlacesToVisit that returned a list of places, including a description and a name. If we published documentation saying that visiting the `/places` URL should result in a specific JSON response, you could assert that this stayed true with the following contract test:

```
[Test]
public void AllPlacesShouldReturnJsonViaGet()
{
  var places = "http://myservice/places"
      .GetJsonFromUrl()
      .FromJson<PlaceResponse>();
  Assert.IsTrue(
      places.Places.Any(p => p.Name.Equals("Toronto")));
}
```

The utilities follow a nice fluent syntax - you define a string containing a URL, call `GetJsonFromUrl()` which is an extension method on `String`, and then pass the result to `FromJson<T>`. You pass in the type that you’re expecting the service to return; `FromJson` will deserialize the JSON response and return an object of the expected type.

The test above will send the following request to the server:

```
GET http://myservice/places HTTP/1.1
Accept: application/json
Accept-Encoding: gzip,deflate 
```

## Passing Parameters

If we need to pass in parameters on the URL, we can do that too:

```
[Test]
public void GetPlacesWithIdShouldReturnSpecificPlaceJson()
{
  var shouldBeToronto = "http://localhost:28192/places/{0}"
      .Fmt(4)
      .GetJsonFromUrl()
      .FromJson<PlaceToVisitResponse>();

  Assert.AreEqual(
      "Toronto",
      shouldBeToronto.Place.Name);
}
```

The `Fmt()` extension method to String does essentially what String.Format would do, replacing the `{0}` with `"4"` in this case.

## HTTP Post

If you’re trying to POST to a service, providing a request DTO is simple as you might imagine:

```
[Test]
public void PostingJsonToPlacesShouldReturnAndCreateNewPlace()
{
  var testPlace = new Place
                      {
                        Name = "London",
                        Description = "Capital of UK"
                      };

  var response = "http://myservice/places"
      .PostJsonToUrl(testPlace)
      .FromJson<CreatePlaceResponse>();

  Assert.AreEqual(
      testPlace.Name,
      response.Place.Name);
}
```

`PostJsonToUrl` follows the same fluent syntax as `GetJsonFromUrl`, but optionally accepts a DTO object, which it will serialize it and pass it in as JSON like so:

```
POST http://myservice/places HTTP/1.1
Content-Type: application/json
Accept: application/json
Accept-Encoding: gzip,deflate
Host: myservice
Content-Length: 54
Expect: 100-continue

{"Id":0,"Name":"London","Description":"Capital of UK"}
```

While I like `JsonServiceClient` for its flexibility, I prefer the lower level HTTP Utilities when I need to be specific.
