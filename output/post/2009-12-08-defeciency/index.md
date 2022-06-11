---
title: "Deficiency"
date: "2009-12-08"
categories: 
  - "technology"
tags: 
  - "entity-framework"
  - "linq"
---

The promise of LINQ is that beautiful, expressive syntax once you have your tables nestled away in the ORM. Instead of queries being, essentially strings- dead, lifeless offerings that need to be burned on the SqlClient altar in hopes that they reach the far away distant ears of the SQL Server gods, queries are alive in the hands of the developer. We can work with them, debug them, auto-complete them, tune them, refactor them just as we would any code.

[LinqPad](http://www.linqpad.net/) deserves a special mention here- this software is brilliant for getting to know the LINQ syntax. I paid the $20 upgrade ten minutes after installing - the last time I did that was for Ultraedit's column mode- and I can tell you, it's that good.

However the more I work with LINQ and Entity Framework, I wonder how pervasive it can or should be. Let's look at some real world scenarios:

 - Medium scale application doing financial processing 
 - Multi-database environments where you might need to query across 50+ databases 
 - complex reporting needs, data marts, large scale transformation

For instance, as it stands with Entity Framework if you want to use a stored procedure you have to perform the following steps:

1. Create your data Model
2. Import the stored procedure (this does not happen by default!)
3. Tell Entity Framework what kind of value the stored procedure is expected to return
4. Write code to set up a database handle, start a new command, reference your stored procedure, add parameters to it one by one, and finally execute. (If this is sounding a lot like SqlClient, you'd be right, except it's Entity Framework's backwards cousin of SqlClient and you don't even have syntactical niceties like command.Parameters.AddWithValue() - you have to build each parameter and add it to the command one by one)

It's actually easier in SqlClient. You can check out what I'm talking about [here](http://social.msdn.microsoft.com/forums/en-US/adodotnetentityframework/thread/44a0a7c2-7c1b-43bc-98e0-4d072b94b2ab/).
