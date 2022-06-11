---
title: "WCF: A Better Web Service?"
date: "2009-12-16"
categories: 
  - "technology"
tags: 
  - "activemq"
  - "asynchronous-messaging"
  - "wcf"
---

According to Microsoft:

> **Windows Communication Foundation is... a part of the .NET Framework that provides a unified programming model for rapidly building service-oriented applications that communicate across the web and the enterprise.**

**If you've talked to me about technology or read my blog, you'll know I'm a big fan of asynchronous, queue based messaging architectures. The typical SOAP scenario leaves a [lot to be desired](http://paymentnetworks.wordpress.com/2009/08/06/aynchronous-messaging-vs-rpc/), in my estimation:**

1. It doesn't guarantee messages will arrive in order
2. It doesn't guarantee that messages will arrive at all, really
3. It leads to development patterns that make it difficult to handle messaging failure

So I promised my new Microsoft buddies that I'd look at WCF, and not force them to go understand [ActiveMQ](http://activemq.apache.org/), my preferred FOSS Broker.

First of all, I can say that the Channel9 [Endpoints video](http://channel9.msdn.com/shows/Endpoint/Endpoint-Screencasts-Creating-Your-First-WCF-Service/) by Pluralsight was a great experience, as was the Visual Studio 2008 .NET 3.5 "New WCF Service Library" code automation. If you break it down in to small parts, you see that you can _very_ quickly (ten minutes or less) go in and say "this is my Interface, this is my class, this is its method, make me a service..." and it will do it all for you. Even cooler, you can very easily add a "reference" to this service right in an external ASP.NET web app and it will consume it! When you are used to working with ActiveMQ, and your app has to some how have a middle ground layer between each call to the web page and the actual service client, this is really exciting to have two endpoints on a broker in less than five minutes, let me tell you! But if you check under the covers what do you get?

I'm used to queue URIs like "tcp://x.x.x.x:61616" or "stomp://x.x.x.x:61617" or "ssl://x.x.x.x:61618". The URI for my little Visual Studio queue is http://localhost:8374/Design\_Time\_Addresses/MyServices/Service1/mex. What's that exactly?

It isn't a plain SOAP service, thankfully. It isn't asynchronous, and it is HTTPS based. Via configuration options,  TCP/IP (similar to OpenWire)  is an option.  How is it better than bare web services?  Well, WCF is an architecture that lends itself to smart programming - and more to the point, its easy to migrate to [different bindings](http://www.code-magazine.com/article.aspx?quickid=0605051&page=3), [including MSMQ](http://code.msdn.microsoft.com/msmqpluswcf).

Back to the Visual Studio experience, I am very impressed with how quickly it comes together. And to put the icing on the cake; press F5 and Visual Studio automatically sets up a WCF client, similar to what we would have done with [JConsole](http://java.sun.com/developer/technicalArticles/J2SE/jconsole.html) or Hermes to inspect an ActiveMQ broker - but furthermore, it makes it easy to interact with your "WCF Service" by hand.

Now, I'm sure the smart folks at Microsoft have made it so that WCF is an improvement on SOAP services, but all of this is starting to sound a little bit _too_ easy if you ask me.

Luckily, WCF is not a one trick pony: you can also [bind it to](http://msdn.microsoft.com/en-us/magazine/cc163394.aspx) MSMQ, TCP, even NamedPipes if you want. The other thing you can configure is whether or not you want your service to be asynchronous or synchronous.

The more I dig, I start to wonder if I'm almost better off sticking to a plain queue broker - no fancy automatic code generation and test clients... at least I'll know exactly what I'm working with. But, so far, my first impressions of WCF are positive.

Here's some code examples for the curious:

```
[DataContract]
public class Heartbeat
{
     [DataMember]
     public string Host;
     [DataMember]
     public string Id;
}

[ServiceBehavior(InstanceContextMode=InstanceContextMode.Single)]
    public class HeartbeatService : IHeartbeatService
    {
        List heartbeats = new List();

        #region IHeartbeatService Members

        public void SubmitHeartbeat(Heartbeat hb)
        {
            hb.Id = Guid.NewGuid().ToString();
            heartbeats.Add(hb);

        }

        public List GetHeartBeatRecords()
        {
            return heartbeats;
        }

        #endregion
    }
 [ServiceContract]
    public interface IHeartbeatService
    {
        [OperationContract]
        void SubmitHeartbeat(Heartbeat hb);
        [OperationContract]
        List GetHeartBeatRecords();

    }

```
