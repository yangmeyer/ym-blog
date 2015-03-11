---
layout: post
title: "Empowering Infrastructure"
date: 2012-03-23 12:18
comments: true
categories: 
---
These are wonderful times for making software. In a day or two, you can take a project from an idea in your head to the fully functional and deployed product that people anywere can use.

Programming is really about expressing your ideas in code. This is why it’s really gratifying to build on powerful frameworks like Rails, jQuery, or the Cocoa Touch family: They do all the low-level minutiae so you can focus on the high-level description of your ideas.

Now something similar has been happening for the infrastructure that allows us to get our software to the users.

Three examples
------------
This wasn’t always so. (I feel old talking about stuff I did *ten* years ago… but what the heck.)

**Blog engine** — I remember that, when ten years ago I started a personal blog, I built an engine from scratch. I learned about the low-level standards and used them and nothing more. The content was saved in a flat RSS file and converted by a hand-crafted XSLT into XHTML, sprinkled with some nice CSS. (I mean, who—except perhaps for some enterprise BPEL types—still really writes XSLT manually?)

It all felt like excruciatingly low-level chores even ten years ago… Admittedly, I’m sure even then blogging engines must have already existed (maybe Wordpress) and part of me just wanted to learn about all these technologies. But it did not at all feel ridiculous to build something up from the foundations.

Fast-forward to today: For this blog, I simply install the delightful [Octopress](http://octopress.org/) engine on my machine, write a post in minimally intrusive [Markdown](http://daringfireball.net/projects/markdown/), run a rake command, and there’s my finished and baked blog.

**Deployment** — Back in 2006 I had much fun making an app with Ruby on Rails. But I really struggled with the deployment and operations parts. Turned out that for a high-performance server running my Rails app I would have had to dive into load balancing and the whole shebang of fiddling with the server, all of which was “merely” about getting the app to the users, instead of letting me *create.*

Now look at us today. Thanks to the amazing power of [Heroku](http://www.heroku.com/), making my blog available to the world was a simple matter of signing up, setting up the API key, and doing `git push heroku master`.

As for **deploying mobile apps**, the App Store is a godsend for hobbyists and indie developers. Once you understand the code-signing process, it's comparably easy to just publish your app. (And before releasing it, I can distribute beta versions of my apps with [TestFlight](http://testflightapp.com).)

**Data backend** — I remember the intricacies of configuring and administering a web app on a LAMP stack, as one of the main developers of a regionally targeted Web startup circa 2003–2004. This was before PHP knew about objects, and we had to make sure the database didn’t jumble up its encoding, and check that a PHP or MySQL upgrade didn’t break anything, and all this other onerous configuration stuff that was necessary to offer our users a reliable and fast web app. Necessary, yes. Fun, no.

In contrast, this week a colleague and I had this idea of a small-scale-social “matchmaker” app for encouraging two random people from our company to go have lunch together. So I quickly programmed a proof-of-concept iPhone client app, and used [Parse](http://parse.com) for saving and retrieving data somewhere in the cloud, and for sending push notifications. And bam! before I knew it, I had a fully working client–server system.

*I could hardly believe it.* Not so long ago, just setting up the database tables and mapping it to objects would have taken me more time than that.

In short
----------
Heroku, Parse, Octopress, the App Store, TestFlight.[^1] There’s a pattern here. They are all infrastructure providers that empower us software makers to very very quickly turn an idea into a marketable product. By letting us “just use” their services, they let us focus on creating—on putting our idea into code.

Let me know if you think of any other services that similarly accelerate development. I’m [@yangmeyer](http://twitter.com/yangmeyer) on Twitter.

-------------------------------------

[^1]: To be sure, the same could probably be said of comparable alternatives like StackMob, HockeyApp, secondcrack. I just haven’t used them myself.
