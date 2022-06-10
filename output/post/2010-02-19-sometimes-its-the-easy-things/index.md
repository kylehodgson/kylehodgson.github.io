---
title: "Sometimes it is the easy things..."
date: "2010-02-19"
categories: 
  - "technology"
tags: 
  - "testing-visualstudio"
---

When adding a new reference to a well known payment gateway's web services WSDL URL, I was confused tonight when I kept getting the following error over and over:

`Could not find endpoint element with name "PaymentGateway" and contract "PaymentGateway.ContractName" in the ServiceModel client configuration section. This might be because no configuration file was found for your application, or because no endpoint element matching this name could be found in the client element.`

I couldn't wrap my head around it; I had let Visual Studio set up the reference with Add Service Reference. Why wasn't it working?

I had forgotten that when you are unit testing a class that uses a service reference, VisualStudio's built in test harness doesn't read the app.config from the project your target class is in. You have to copy the serviceModel entries to the app.config in your test project as well.
