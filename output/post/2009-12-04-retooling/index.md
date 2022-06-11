---
title: "Retooling"
date: "2009-12-04"
categories: 
  - "technology"
tags: 
  - "asp-net"
  - "entity-framework"
  - "linq-to-sql"
  - "visual-studio-2008"
---

The beginning of a software project can be a great time to look at your tools with fresh eyes. Sure, you've been doing just fine all along with Visual Studio 2005 for years, but all right, fine now that 2010 is in beta it's probably time to take a hard look at Visual Studio 2008.

Let me tell you, I feel as though I have awoken from a deep sleep. I've had my head down managing projects and launching programs for so long that I feel like a decade or more has passed. You have to keep in mind, I wrote a lot of code that looked like this:

```vb
SqlCommand scGetResults = new SqlCommand();
scGetReults.Connection = dbConn;
scGetResults.CommandType = System.Data.CommandType.StoredProcedure;
scGetResults.Parameters.AddWithValue("@programid", argProgrmId);
DataReader drResutls = scGetResults.ExecuteReader();
if (drResults.HasRows() ) {
     while ( drResults.Read() ) {
          // do something
     }
}
```

And really, this doesn't look a lot different from the PHP I was writing five years ago, with the exception of parameters:

```php
mysql_connect("localhost", "mysql_user", "mysql_password") or
die("Could not connect: " . mysql_error());
mysql_select_db("mydb");
$result = mysql_query("SELECT id, name FROM mytable");
while ($row = mysql_fetch_array($result, MYSQL_NUM)) {
 printf("ID: %s  Name: %s", $row[0], $row[1]);  
}
 
```

So to go to StackOverFlow DevDays in October and see a live coding demo using LINQ I was blown away! I mean how much cooler is that?

```csharp
var myList = (
     from c in \_db.CustomerSet
     where c.Account.Balance > minAccountBal && c.Program.OwnerId == i
     select c).ToList();
```

Upon further inspection, LINQ can't be used everywhere - our app requires access to hundreds of databases (scaling to thousands) using stored procedures; and, well, Entity Framework does not support stored procedures very nicely yet. LINQ-to-SQL does, but Microsoft has made [noises](http://stackoverflow.com/questions/973506/asp-net-mvc-linq-to-sql-or-entities) that seem to indicate it won't be supported in future.

I have heard they're fixing that in .NET 4.0, which ships with VS2010... something tells me I won't be holding my breath for its release. I am _loving_ ASP.NET/MVC however!
