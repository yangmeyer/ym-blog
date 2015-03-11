---
layout: post
title: "Some observations from coding Snipster"
date: 2012-10-18 17:32
comments: true
categories: 
- Snipster
- iOS dev
---
<a href="http://itunes.apple.com/us/app/snipster-collect-+-arrange/id568099336"><img src="{{ root_url }}/images/Snipster-57-rounded@2x.png" alt="Snipster" style="background:white; float:right; margin:0 0 1em 1em" /></a>

In the process of implementing [Snipster](https://itunes.apple.com/us/app/snipster-collect-+-arrange/id568099336), my newest app creation (see the [announcement post](/blog/2012/10/18/snipster-is-here/)), I ran into some interesting problems and tried out some new frameworks and features. What follows are some observations, in no particular order:

After having quickly set up **Core Data** (it’s a breeze!) and then running into a dead end with ordered relationships and NSFetchedResultsController,[^1] I now appreciate better why Core Data is both revered and despised.

[mogenerator](https://github.com/rentzsch/mogenerator) taught me how to use [Mike-Ash-style](http://www.mikeash.com/pyblog/friday-qa-2011-08-19-namespaced-constants-and-functions.html) **namespaced constants**. I love it!

**Auto Layout** in iOS 6 is a neat concept in theory, but fiendishly fickle in practice. Interface Builder keeps re-interpreting what I want it to do. And when you’re done, you’re still not safe from inconsistency problems.
 
**Unwind segues** are a great time saver in general, but [don’t work as expected](http://stackoverflow.com/questions/12665818/unable-to-create-unwind-segues-when-using-custom-view-controller-containment/) with custom view controller containers.

I love how Apple continues to incrementally improve on existing APIs. Just take the **simplified dequeuing of table-view cells**, which in iOS 5 was only available for storyboard prototype cells.

It’s also simply awesome (in the sense of less code, cleanear code, better code) to **get rid of those viewDidUnload methods.** I never really saw the sense in `-viewDidUnload` ever since I started using ARC and `weak` IBOutlets for my subviews (which were retained and thus owned by their superview and when deallocated automatically got their references nilled).

Making a **fully custom Back button** is harder than expected. (No, just setting a custom UIButton as your `navigationItem.leftBarButtonItem` doesn’t cut it – look at [how the Back button transitions during navigation](http://watchingapple.com/2009/11/a-closer-look-at-iphone-transition-animations/).)

Comment via [Twitter](http://twitter.com/yangmeyer) or [App.net](http://alpha.app.net/yangmeyer).

-------------------------------------
[^1]: While researching for this blog post, a mere two weeks later, I now found a [promising clever approach in a StackOverflow entry](http://stackoverflow.com/questions/9875557/core-data-ordering-with-a-uitableview-and-nsorderedset).
