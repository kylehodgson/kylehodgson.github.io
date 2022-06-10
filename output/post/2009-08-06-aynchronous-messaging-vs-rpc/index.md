---
title: "Aynchronous Messaging vs. RPC"
date: "2009-08-06"
categories: 
  - "technology"
tags: 
  - "activemq"
  - "architecture"
  - "asynchronous-messaging"
  - "soap"
---

So we get this fairly often - a new partner, or client - that wants to load cards with us. Maybe load it with loyalty points, or maybe with cash. No problem - that's what we do. I was actually on the phone with one gentlemen today who reacted very strongly to my suggestion of a simple FTP based batch file in horror: "Why should we use such an antiquated system as FTP to exchange information? Can we not do some sort of SOAP query here?"  You could tell this guy was a technology manager and an effective executive, had a great hammer and was hoping I was a nail. I can relate, I'm often in his shoes.  And to top it all off, he wasn't taking any of this low tech 1970's FTP antiquted technology he wanted something modern!

OK, so first of all of course we can. We're a modern .NET shop here, developers on VisualStudio of a recent vintage, we do SOAP calls all the time with partners - LAMP stack nusoap clients, Java clients, AS400 shops calling java to talk to SOAP, you name it we've done it.  It works, it's a known quantity.

So lets break this down in to pieces.  We're going to need a web service to expose a function to put money on the card.  Simple!

LoadCash(int clientid, decimal amount, int transactionid)

Now, what happens if there is some flaky internet connection moment happening right during this call? Maybe for some reason it takes us 61 seconds round trip, and they time out their SOAP call at 60 seconds. What's the status? If they had waited one more second, I would have told them "the load worked, thanks, please go about your business" but the Internet happened, and now they didn't get the return code.

Should they try again?  If they don't, their cardholder will be unhappy - they didn't get paid. If they do, they're overpaying the cardholder.  No problem, no problem just put something in there where... let's see... where we can check on a transactionid later...  OK, fine, but now you have to maintain a table for those. No problem disks are cheap we'll just put it in...

What I wish everyone wanted, instead of bloated ASCII XML file exchanges with no good mechanism to guarantee delivery, was an asynchronous messaging system, namely something like JMS. We use ActiveMQ here, and let me just say I'm a big fan.  This should come as no surprise, I served billions of hits on Apache in my UNIX admin days.

I just don't think all that many technology folk know about message queues, and when I mention them I usually get a blank stare.

So let me be the first to point out to you that Message Queues are a wonderful thing: built in to every message is the assumption that you will need to know beyond the shadow of a doubt whether or not the message will arrive. And the message will arrive! It's the broker's job to guarantee it.  If the DB or Application server falls over and is disconnected for two hours, or if the partner's $20 wifi office router firewall and VPN client hangs because the guy in marketing turned on his bit torrent client again, it does not matter. As long as the client is able to enqueue in to a broker, once connectivity is restored the next thing it's going to see is that two hour old message asking us to load the card.  Guaranteed!

Sure, it means you have to think a little bit more about what you are going to do, and you might have to write a little bit more code up front to deal with the asynchronous nature of this architecture. But its a false economy to say SOAP is cheaper - you still end up having to write all that code, and half of the code that's in a modern MQ broker too and now you'll have to maintain it all.

What am I talking about? You don't have to write all this code to try to clean up every possibility:  results = paymentCard.LoadCard(123,5.74,1001), if results.Length=0 then results = paymentCard.queryPreviousLoad(1001), if results.Length=0... wait a minute, OK this should be a recursive function... but we should set an increment on this, if we have tried 60 times then... what, email customer service? No, email me... at 3 AM... to tell me we tried to load a card and didn't get an answer after 60 tries. Right, OK... and put it in a table of unhandled exceptions for follow up, there could be a report for that... users could log in and view any failed requests, or we could schedule something else to try them all again the next day...

There's an old saying in developmis ent: Ugly + Hard = Bad.  Something can be ugly, and still be good - something can be hard to write, and still be good - but if its both ugly and hard to write you are probably doing it wrong.  Let me assure you, the promise of a simple RPC call to a remote server over the Internet is sometimes an empty promise.  Things can go wrong.  So plan ahead and choose the right technology for the job.

See the problem with SOAP, is that it leads the programmer to believe that when he makes a call like:

> var returnVal = SomeOtherNamespace.MethodCall(args)

That nothing can go wrong.  This little one line of code, however, requires a staggering amount of technology to go right before it can be trusted. The developer feels like its a synchronous call with a response... he has forgotten that Jim in Marketing usually downloads torrent files to take him as soon as his boss leaves for the day and that's right about now, so you can forget about returnVal being what you expect it to be.

So back to lowly FTP: no, I'm not in love with coding batch processors for CSV files either.  But at least this is a well documented quantity - and it certainly does not leave it as much to the programmer's imagination as to what can go wrong.  My best advice: using lots of SOAP, and a little bit frustrated about how the corner cases are always uglier than you thought they should be?  Check out message queues and potentially save yourself a lot of trouble.
