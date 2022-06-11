---
title: "Google: The DNS Tzar?"
date: "2009-12-04"
categories: 
  - "technology"
tags: 
  - "dns"
  - "google"
  - "interweb"
---

As far as technology fans go, I like to think of myself as healthily skeptical. Sure, I can love Open Source while seeing that until Ubuntu it did not have a viable option for the client. I can hate Microsoft licensing and still think SQL Server is an excellent platform.  I can love Apple and never even consider buying their laptops as they simply cost twice what I am willing to pay, even if they are worth it.

Google is harder, as they are sooo big. I've been critical of their Android platform - the approach seems too fragmented to compete with the iPhone for instance.  I've admitted on several occasions I'm not smart enough for their wave platform. While I'm a heavy GMail user, I hate the threaded mail display.  All in all, I'd say I am very impressed with Google; but they are, after all, a corporation made up of humans. They will have good days and  bad days. So how do I feel about handing over my DNS keys to them?

The idea of a mono culture comes to mind.  It was one thing to simply decide to rely on Google whole hog for search; there were still competitors, they just weren't as good.  Search is a user iterative process: the user knowingly enters a search term, sees results, and makes a _choice_ to click one of them. If the results are terrible day after day, we'll start going to Yahoo or Bing.

The same doesn't apply to Google DNS. I imagine that countless admins across the world have started hard wiring primary and secondary DNS settings on their DHCP servers to 8.8.8.8 and 8.8.4.4, if only for testing.  The users of those systems do not know or understand what's going on here, and its the kind of choice that once wired in isn't likely to be changed by the legions of computer users as easily as a search provider.

I don't suspect Google will aim to do a bad job; and in fact, I suspect they'll do a better job than some ISPs do.  But just as those ISPs ditched NNTP and allowed Google Groups to take on that weight, will corporations stop running their own DNS caches? Will ISPs just start deferring to 8.8.8.8 where they are not authoritative?  It wont even matter if most DHCP servers issue 8.8.8.8 and 8.8.4.4 as the Primary and Secondary. Clients won't even bother to check with the ISP's DNS server anymore - they're just going straight to the source.

The hierarchical, cached nature of DNS is part of why the Internet is successful. Your PC asks your network Primary DNS server, that server asks most likely an upstream ISP DNS server, that one asks the root who is authoritative for your query, which provides the answer - and every step below will cache the response for 24 hours; meaning that they won't need to repeat that query for a while - if some one asks, they can just dig up the answer out of the cache. 8.8.8.8 takes half of those steps out of the equation.

If 8.8.8.8 was the DNS server for, say, 35% of the Internet - and they wanted to offer a "visit suggestion" tool, for instance something like "You typed www.kevsoftware.com, did you really mean www.competitor.com?" - this would be way too easy.  "But", you say, "Google is my friend! They wouldn't do that!" well, I suppose I believe that too, for now. But they are after all a publicly traded company. Will they inevitably need to bow to shareholder pressure to boost revenue?  I mean, once they have completely dominated all the markets they operated in, where else will they turn?  "Oh hey guys, remember how we got everyone to switch to 8.8.8.8 back in 2009? Well, we could...."

It has the potential for benefit now, but it creates the possibility for sooo many problems down the road.  It's funny, I happily use Google Docs, Google Calendar, GMail, tons of Google software without concern. But the second they touch DNS, I'm nervous.

If they wanted to start a DNS super pool; something like [pool.ntp.org](http://www.pool.ntp.org/) where they were heavily featured, but not in total control, and actively inviting participation from other giants - say IBM, the US government, Microsoft , or heck even just find some way to work with the [DNS root servers](http://www.root-servers.org/).  It's just too much control in one place, I think.  It's just not very much like the interweb I've come to know and love.
