---
title: "Is the Nexus 4 in stock?"
date: "2013-01-15"
categories: 
  - "technology"
tags: 
  - "crontab"
  - "ruby"
  - "twitter"
---

I’m currently a bit obsessed with the LG Nexus 4. The price tag, specs and design seem to be a fairly amazing combination. The trouble is, however, it’s been available in Canada for a grand total of ten minutes from what I can tell.

To combat the problem, a [friend](http://www.twitter.com/tazsingh) reminded me on Twitter that I am software developer, and that I can automate things. I set to work, and arrived at the following solution:

- A command line script that checks the Nexus 4’s web page to check if its in stock.
- If it is, this uses a Ruby script called "t" to send me a direct message
- Direct messages are desirable for me as they reliably get to my phone

**Command Line to Check Google Play to See if the Nexus 4 Is In Stock**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt><b><font color="#0000FF">test</font></b> <font color="#009900">$(</font>curl -s https<font color="#990000">:</font>//play<font color="#990000">.</font>google<font color="#990000">.</font>com/store/devices/details<font color="#990000">?</font><font color="#009900">id</font><font color="#990000">=</font>nexus_4_16gb <font color="#990000">\</font>
  <font color="#990000">|</font> tr -d <font color="#FF0000">'</font><font color="#CC33CC">\n</font><font color="#FF0000">'</font> <font color="#990000">|</font> grep -vi <font color="#FF0000">"sold out"</font> <font color="#990000">|</font>wc -l<font color="#990000">)</font> -eq <font color="#993399">1</font> <font color="#990000">&amp;&amp;</font> <font color="#990000">\</font>
  t dm @yourtwitter <font color="#FF0000">"check google play"</font></tt></pre></td></tr></tbody></table>

The one thing I’m worried about; the machine I’m running this on sits in the US, and I’m concerned that there might be stock for Canadian clients but not US, and vice versa, so I’m running it on my MacBook too for good luck.

To get this to work, your crontab needs to set up a decent Ruby environment; mine looks like this (though I’m certain that this could be improved):

**My Crontab**

<table border="0" bgcolor="#e8e8e8" width="100%" cellpadding="10"><tbody><tr><td><pre><tt>kyleh@vm<font color="#990000">:~</font>$ crontab -l
<font color="#009900">rvm_bin_path</font><font color="#990000">=</font>/home/kyleh<font color="#990000">/.</font>rvm/bin
<font color="#009900">GEM_HOME</font><font color="#990000">=</font>/home/kyleh<font color="#990000">/.</font>rvm/gems/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>
<font color="#009900">IRBRC</font><font color="#990000">=</font>/home/kyleh<font color="#990000">/.</font>rvm/rubies/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font><font color="#990000">/.</font>irbrc
<font color="#009900">MY_RUBY_HOME</font><font color="#990000">=</font>/home/kyleh<font color="#990000">/.</font>rvm/rubies/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>
<font color="#009900">PATH</font><font color="#990000">=</font>/home/kyleh<font color="#990000">/.</font>rvm/gems/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>/bin
<font color="#009900">PATH</font><font color="#990000">=</font><font color="#009900">$PATH</font><font color="#990000">:</font>/home/kyleh<font color="#990000">/.</font>rvm/gems/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>@global/bin
<font color="#009900">PATH</font><font color="#990000">=</font><font color="#009900">$PATH</font><font color="#990000">:</font>/home/kyleh<font color="#990000">/.</font>rvm/rubies/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>/bin<font color="#990000">:</font>/home/kyleh<font color="#990000">/.</font>rvm/bin
<font color="#009900">PATH</font><font color="#990000">=</font><font color="#009900">$PATH</font><font color="#990000">:</font>/usr/local/sbin<font color="#990000">:</font>/usr/local/bin<font color="#990000">:</font>/usr/sbin<font color="#990000">:</font>/usr/bin<font color="#990000">:</font>/sbin
<font color="#009900">PATH</font><font color="#990000">=</font><font color="#009900">$PATH</font><font color="#990000">:</font>/bin
<font color="#009900">RUBY_VERSION</font><font color="#990000">=</font>ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>
<font color="#009900">GEM_PATH</font><font color="#990000">=</font>/home/kyleh<font color="#990000">/.</font>rvm/gems/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>
<font color="#009900">GEM_PATH</font><font color="#990000">=</font><font color="#009900">$GEM_PATH</font><font color="#990000">:</font>/home/kyleh<font color="#990000">/.</font>rvm/gems/ruby-<font color="#993399">1.9</font><font color="#990000">.</font><font color="#993399">3</font>-p<font color="#993399">194</font>@global
<font color="#990000">*</font> <font color="#990000">*</font> <font color="#990000">*</font> <font color="#990000">*</font> <font color="#990000">*</font> <b><font color="#0000FF">test</font></b> <font color="#009900">$(</font>curl -s https<font color="#990000">:</font>//play<font color="#990000">.</font>google<font color="#990000">.</font>com/store/devices/details<font color="#990000">?</font><font color="#009900">id</font><font color="#990000">=</font>nexus_4_16gb <font color="#990000">\</font>
 <font color="#990000">|</font> tr -d <font color="#FF0000">'</font><font color="#CC33CC">\n</font><font color="#FF0000">'</font> <font color="#990000">|</font> grep -vi <font color="#FF0000">"sold out"</font> <font color="#990000">|</font>wc -l<font color="#990000">)</font> -eq <font color="#993399">1</font> <font color="#990000">&amp;&amp;</font> <font color="#990000">\</font>
 t dm @yourtwitter <font color="#FF0000">"check google play"</font></tt></pre></td></tr></tbody></table>

Installing t is simple, just gem install t. The first time you run t, just do t authorize and it will walk you through authorizing t to talk to Twitter for you.

Enjoy!

**References**

- The [GitHub](http://sferik.github.com/t/) for t
- [The Nexus 4](https://play.google.com/store/devices/details?id=nexus_4_16gb)
