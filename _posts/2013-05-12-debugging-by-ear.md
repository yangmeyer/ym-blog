---
layout: post
title: "Debugging by Ear"
date: 2013-05-12 16:26
comments: true
categories: 
- iOS-Dev
---

Here is a neat little trick for your debugging toolbox.

<!-- more -->

Most of us have done “debugging by printing to console”. I know *my* PHP code was often littered with `echo` statements during development, my Java code sometimes needed to be ridded of `System.out.println` calls before <del>pushing</del> committing it to CVS, and even now I sometimes use `NSLog` for debugging weird concurrency problems.

## Better logging with breakpoints

With Xcode, there is a better way to log out text markers (“made it here!”) and values (“content offset: (0.0, 88.0)”). Create a breakpoint, choose **Log Message** or **Debugger Command**, specify what you’re interested in, and let Xcode **Automatically continue after evaluating**.

![Log messages using breakpoints](/images/in-posts/2013-05/Xcode-debugging-message.png)

For example, with the Log Message breakpoint action you could type:

	content offset: @(CGPoint)[self.tableView contentOffset]@

With the Debugger Command breakpoint action, you could use:

	p (CGPoint)[self.tableView contentOffset]

During development, this is much better than `NSLog` calls. It doesn’t clutter your code, you can disable the logging individually and temporarily, and you can modify the log output without recompiling.

## Let there be sound

Sometimes, though, this logging approach isn’t optimal. Say you’re debugging a custom caching solution and you’re interested in when the cache misses happen. The classic approach: 1.) use the app, 2.) log out the cache misses, 3.) piece together *ex post facto* the cache faults with the actions that triggered them (or divide your focus between the simulator/device and the logging console).

I have found that in this case it is much more useful to use another of the Breakpoint Actions called **Sound**. It lets you play a short sound (Ping, Tink, Pop, …). In combination with “Automatically continue after evaluating”, this means that I can use the app and **get the debugging info in realtime**, without having to switch my focus.

## Tick tock (or rather: \*ping\* \*plop\*)

I most recently used this for debugging a problem while using the built-in `UIActivityViewController`. I had relied on the assumption that presenting and dismissing of the mail/tweet compose screens would call the appropriate view appearance methods on my presenting view controller. Strangely this was not the case (`-viewWillDisappear:` is not called). I was key-value-observing a subview and thus did not symmetrically remove the view controller as an observer, so the app crashed.

![Let breakpoints play a sound](/images/in-posts/2013-05/Xcode-debugging-sound.png)

So I had my breakpoints play a “Ping” sound whenever the view controller added itself as an observer, and a \*plop\* sound (“Bottle”) whenever it removed itself. In the end, I could *hear* whether I had exactly one \*plop\* for a \*ping\*.

Give it a try. It’s useful, and fun.
