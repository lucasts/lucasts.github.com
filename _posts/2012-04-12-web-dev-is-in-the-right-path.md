---
layout: post
title: Is Web Dev in the right path?
---

{{ page.title }}
================

<p class="meta">12 Apr 2012 -  Rio de Janeiro</p>

Preface
-------

Some days ago, [I twitted about having MVC in both sides of development, server and client](https://twitter.com/#!/lucasts/status/189476751943024641)

<blockquote>"So, we ended up with MVC in server side and client side. Is that good or bad?"</blockquote>

days after that I  was reading about [Meteor](http://meteor.com/).

Can things like [Meteor](http://meteor.com/) be  the solution?

Wait, is there any problem that need a solution?


The Old, The New and What we are doing today
--------------------------------------------

Bringing some history to the table, in a not so long time ago, when web development was growing, the client side was not that important 
in developers point of view and a big mess of HTML, JS, CSS, Tables and so on was not major problem.

On the server side, we can split things in amateur to regular business using vastly PHP and ASP solutions and Java on bigger companies/apps.

After some years of transition,  in the client-side, we walk across many movements, like tableless, semantic web, better javascripts libraries.  In the server, 
frameworks start to grow in maturity, making development a lot faster. Nice things came out like Rails, Django and then some small revolutions are poiting
to a good mix of fast prototypes with those frameworks and a concern about HOW to make things better with speed.  This bring 
programming patterns(think Rails 1 and Rails 3) to web development.

As today, a new project for a web application can be started fast, but with good pratices both server and client side.
You pick some language and a framework that fits your taste to do the server side code and do the same in the client, 
finding a good MVC JS framework. Libs like [Backbone](http://documentcloud.github.com/backbone/), [Ember.js](http://emberjs.com/) besides some differences they try to bring a better code separation on client
implementation.

But remember the cited tweet in the preface, Is that good or bad?

Let's recape web development today:

1) A database
2) A script language with a full stack MVC framework on the server side
3) A MVC framework on the client side
    
Yes, Mister hater back there, other methods exists, some looking old and other new, but on average is that.

So, there is a problem here?
----------------------------

Short answer is: No

To begin we are always evolving, there is not definitive things in life and development isn't a exception for sure.
But we are certainly experiencing some good transitions or at least I think that we reach a point we need to hard move to really
achieve greater way for doing web.

Back to the future
------------------

<blockquote>"We ended up with MVC in both server and client"</blockquote>

That was wrong, we not ended with MVC. We reach this point and we can think it as a good milestone on web development history.
Now we had a better way to do web development, and with the help of those frameworks speed was not harmed.

But, as soon we fix some problem we need to move to another and here are two that will be our concern for the next years. 

- Duplication of code: We need to reproduce much of the server code in client
- Sync of Data: With dup of code, we are using more sophisticated data structures in client we need regularly sync with the server, 
and resolve conflicts if any.

Meteor try to solve this using [node.js](http://nodejs.org/) under the hood, sockets, and code sharing between server and client.

I don't take a look yet to see if it really is good and solve the problem the way they sell it, but the idea itself is very good and match what 
I was think when wrote the tweet.

That's it?
----------

Surely not, Meteor is one of the first implementations trying to solve this problem, but in my mind things can lead to others problems, 
solutions , scenarios.  Here are some random thoughts I'm thinking lately about this matter:

- If sharing code directly between client and server are the final solution, we will need to always use javascript in the server, so that 
is not a real solution.
- Some kind of standard([REST](http://pt.wikipedia.org/wiki/REST)?) for better mapping between sides? Things like data attributes calling back server methods maybe.
- Validation: Time for a standardized way between vendors with callbacks on servers?
- http itself is a problem to apps in web, websockets would be a solution for it, let's see if vendors will implement it.
- Should client(browser) knows how are the entities on a given page and know how to update/sync with the server?
- How make development fast and easier without locking down web itself and not so flexible like today.
- Yet on Infrastructure, how to do it in a safe way.
- How to handle correctly different bandwidth


Conclusion
----------

Web development is moving fast, from client mess years ago we made a better way to do things. We are reaching the maximum that 
we can extract from the infrastructure(http, browsers, languages), but some is there yet to be improved. 

How will we develop in the next years without duplication of code and maintaining choices flexibility? 

Right path? Well, better it be.