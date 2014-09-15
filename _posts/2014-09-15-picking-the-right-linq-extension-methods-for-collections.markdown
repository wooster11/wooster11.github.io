---
layout: post
title:  "Picking the Right LINQ Extension Methods for Collections"
date:   2014-09-15 09:04:50
tags: [linq, csharp, efficiency, for loop]
---
LINQ has been a great feature for .NET developers when it was introduced in the .NET 3.5 Framework. It has quickly become the de facto standard for filtering or retrieving items from list. Typical use of LINQ extension methods on a collection would qualify as elegant code - clear, concise, and easy to maintain.

However, there continues to be some misuse of said LINQ queries which cause inefficiencies in code that don't really need to be there. From what I can tell, it seems that some developers have a fundamental misunderstanding of what is going on behind the scenes of these extension methods.

Keeping with the Object Oriental theme, let's take this list of twenty employees as an example:

| Employee ID | First Name | Last Name | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Employee ID | First Name | Last Name |
| --- | --- | --- | --- | --- | --- | --- |
| 1000001 | James | Nguyen | | 1000011 | John | Chang |
| 1000002 | Edward | Lee | | 1000012 | Chris | Takamoto |
| 1000003 | Michael | Kim | | 1000013 | Brandon | Hsu |
| 1000004 | Frank | Wong | | 1000014 | Thompson | Wong |
| 1000005 | Benjamin | Nakamoto | | 1000015 | Henry | Chan |
| 1000006 | Wendy | Wong | | 1000016 | William | Wong |
| 1000007 | Danny | Kim | | 1000017 | Jennifer | Chu |
| 1000008 | Thomas | Lee | | 1000018 | Erin | Tran |
| 1000009 | Robert | Choi | | 1000019 | Elisa | Kim |
| 1000010 | Christina | Chan | | 1000020 | Nick | Wong |

With a list like this, a common scenario would be to get the first employee with a last name of "Wong".

The common for loop to accomplish this is as follows:
{% highlight csharp %}
//Assume that we already have a list of all the employees in the
//aforementioned list called "Employees"
foreach (var emp in Employees)
{
    if (emp.LastName == "Wong")
        return emp;
}
{% endhighlight %}

As you can see, that is a pretty clear, concise, and efficient for loop. However, with LINQ and lambda expressions, we can cut this down even further and still have it very readable and maintainable. For simple cases like this, there's no reason to have extraneous lines of code which makes LINQ a great candidate to be used for something like this.

Changing it to a LINQ query would look something like this:
{% highlight csharp %}
return Employees.FirstOrDefault(e => e.LastName == "Wong");
{% endhighlight %}
Notice how we can turn the above for loop into a single line statement. And at the same time, it's still very clear as to what the code is trying to accomplish. Now, I don't want to get too much into the making of efficient queries using LINQ. Scott Hanselman has a great write up about making these types of changes here: <http://www.hanselman.com/blog/BackToBasicsMovingBeyondForIfAndSwitch.aspx>

What I want to get into is that most developers seem to want to do the above. They want to make things more efficient, but they end up misusing LINQ queries and in turn make things less efficient. Again, this just may be due to a fundamental misunderstanding behind the magic that LINQ does for you.

Using the same information and rules provided above, I've seen several developers write LINQ queries like the following:
{% highlight csharp %}
return Employees.Where(e => e.LastName == "Wong").FirstOrDefault();
{% endhighlight %}

The above code basically translates to a common for loop that looks like the following:
{% highlight csharp %}
var wongs = new List<Employee>();

foreach (var emp in Employees)
{
    if (emp.LastName == "Wong")
        wongs.Add(emp);
}

return wongs[0];
{% endhighlight %}

As you can see, this is clearly a waste of iterations. Imagine if this was a list of tens of thousands of employees. Even worse, if a Wong was one of the first items in this list, the above would still iterate through the entire list of employees before actually returning back what was required. It would a complete waste and can basically cause an application to become unresponsive.

I like to believe that most developers want to write good and efficient code. But when we're given powerful tools such as LINQ, it can become quite easy to misuse these tools and end up writing inefficient code without even knowing it. With that point said, if a developer doesn't understand what's going on behind the scenes of the technology, they may be better off writing classic code. Basically, write the simple for loop that returns on the first match, rather than writing a bad LINQ query. It's important for developers to understand the basics behind a new technology. I want to emphasize that understanding just the basics is enough for most cases because I don't expect a developer to know the complete ins and outs of a new technology. The new technology is there to help a developer, but it's important to know how to properly use that technology otherwise some bad things may happen.