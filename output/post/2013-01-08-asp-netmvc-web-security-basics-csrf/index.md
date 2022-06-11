---
title: "ASP.NET/MVC Web Security Basics: CSRF"
date: "2013-01-08"
categories: 
  - "technology"
tags: 
  - "asp-net"
  - "asp-net-mvc"
  - "c"
---

Cross Site Request Forgery, which is sometimes known by the acronyms "XSRF" or "CSRF", is a category of attacks against a web site which can be very effective - when applications are vulnerable, it’s possible to create real problems. A common example of CSRF in an online banking application can cause a bank account holder to transfer money out of their account to an attackers - CSRF can cause real problems for people! Facebook, Gmail, and Netflix have all had [problems](http://www.internetnews.com/security/article.php/3835596/Facebook+Hit+by+CrossSite+Request+Forgery+Attack.htm) with CSRF.

* * *

## How CSRF Works

Imagine an attacker creates a webpage at www.shadyfreeipad.com that encourages a user to click a link - the link could be pointed at www.vulnerablebank.com/transfer.aspx?amount=1000&to-acct=attacker. If our user doesn’t bank at Vulnerable Bank, or isn’t logged in to online banking, the attack won’t do much - but if our attacker is able to target people who do bank there and understands social engineering, it’s trivial to create a scenario whereby the attacker stands to gain substantially.

**Anatomy of a CSRF Attack**  
![images/mvc-sec-1.png](images/mvc-sec-1.png)

The trick relies on the fact that the user is legitimately logged in to your site, and they don’t know that when they click the link on the attacker’s site that it’s going to make use of a session in another tab to do harm.

* * *

## How to Protect Against CSRF

Web forms users are protected by default in most some scenarios, however, with ASP.NET/MVC you have more responsibility. Luckily, the framework includes a feature that help you secure your site against this kind of vulnerability - but its important to note that you need to actually enable the features.

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Note</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><p><b>Securing ASP.NET Web Forms</b></p>Web Forms is not <em>completely</em> protected by default. More information on how to mitigate <a href="http://haacked.com/archive/2009/04/02/csrf-webforms.aspx">this vulnerability</a> can be found <a href="http://www.hanselman.com/blog/ViewStateUserKeyMakesViewStateMoreTamperresistant.aspx">online</a>.</td></tr></tbody></table>

When you begin thinking about CSRF, you realize that the POSTS of the attack would be given away by their HTTP [referer](http://en.wikipedia.org/wiki/HTTP_referer) variable, which leads to thinking on an approach that involves checking the HTTP referer variable. In practice this isn’t a great approach, however. Problems include HTTP referer spoofing, and visitors instructing their browsers not to send the referer to name a few.

* * *

## A Good Solution: AntiForgeryToken

ASP.NET MVC provides the AntiForgeryToken feature to protect against CSRF attacks. Using it is simple, you need to add one line of code to your view, and one annotation on controller actions that you want to protect.

**View Code**

```csharp
<% using(Html.Form("UserProfile", "SubmitUpdate")) { %>
        <%= Html.AntiForgeryToken() %>
        <!-- the rest of the form goes here -->
<% } %>
```

**Controller Code**

```csharp
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
        // your code won't be executed now unless it came from a page
        // that included the AntiForgeryToken() call
}
```

<table style="margin:.2em 0;"><tbody><tr valign="top"><td style="padding:.5em;"><p><b><u>Important</u></b></p></td><td style="border-left:3px solid #e8e8e8;padding:.5em;"><p><b>Post Only Limitation</b></p>The AntiForgeryToken only protects POST calls, not GET. This makes it critical to follow the guidance that a GET should only be used for requests that have no side effects.</td></tr></tbody></table>

* * *

## A More Comprehensive Solution

While the AntiForgeryToken does work, it’s a bit error prone to have to manually put it on each controller action or even on each controller class. What we really want is for the \[ValidateAntiForgeryToken\] annotation to be applied on every POST controller method. Luckily ASP.NET/MVC provides a mechanism for this: you can add to to a collection of filters on the global FilterProviders object. This allows you to target some controllers and not others, adding the needed security feature.

First, we need to implement an IFilterProvider. Below, you can find Phil Haack’s [ConditionalFilter](http://haacked.com/archive/2009/04/02/anatomy-of-csrf-attack.aspx) class. Begin by adding this class to your project.

**ConditionalFilterProvider Class**

```csharp
public class ConditionalFilterProvider : IFilterProvider
{
    private readonly
      IEnumerable<Func<ControllerContext, ActionDescriptor, object>> _conditions;

    public ConditionalFilterProvider(
      IEnumerable<Func<ControllerContext, ActionDescriptor, object>> conditions)
    {
        _conditions = conditions;
    }

    public IEnumerable<Filter> GetFilters(
        ControllerContext controllerContext,
        ActionDescriptor actionDescriptor)
    {
        return from condition in _conditions
               select condition(controllerContext, actionDescriptor) into filter
               where filter != null
               select new Filter(filter, FilterScope.Global, null);
    }
}
```

Then, add code to Application\_Start that adds a new ConditionalFilterProvider to the global FilterProviders collection that ensures that all POST controller methods will require the AntiForgeryToken:

**Add This Code to Application\_Start**

```csharp
IEnumerable<Func<ControllerContext, ActionDescriptor, object>> conditions =
    new Func<ControllerContext, ActionDescriptor, object>[] {
        // Ensure all POST actions are automatically
        // decorated with the ValidateAntiForgeryTokenAttribute.

        ( c, a ) => string.Equals( c.HttpContext.Request.HttpMethod, "POST",
        StringComparison.OrdinalIgnoreCase ) ?
        new ValidateAntiForgeryTokenAttribute() : null
};

var provider = new ConditionalFilterProvider(conditions);

// This line adds the filter we created above
FilterProviders.Providers.Add(provider);
```

If you implement the two pieces of code above, your MVC application should require the AntiForgeryToken for _every_ POST to the site. You can try it on on Phil Haack’s CSRF [example web site](http://code.haacked.com/mvc-2/CsrfDemo.zip) - once protected, the CSRF attack will throw System.Web.Mvc.HttpAntiForgeryException without having to add the \[ValidateAntiForgeryToken\] annotation. This rules out a whole host of "forgetful programmer" related vulnerabilities, and is currently the best approach for eliminating CSRF.

**References**

- [http://www.internetnews.com/security/article.php/3835596/Facebook+Hit+by+CrossSite+Request+Forgery+Attack.htm](http://www.internetnews.com/security/article.php/3835596/Facebook+Hit+by+CrossSite+Request+Forgery+Attack.htm)
- [http://haacked.com/archive/2009/04/02/anatomy-of-csrf-attack.aspx](http://haacked.com/archive/2009/04/02/anatomy-of-csrf-attack.aspx)
- [http://haacked.com/archive/2011/04/25/conditional-filters.aspx](http://haacked.com/archive/2011/04/25/conditional-filters.aspx)
- [http://msdn.microsoft.com/en-us/library/dd504812(v=vs.108).aspx](http://msdn.microsoft.com/en-us/library/dd504812(v=vs.108).aspx)
