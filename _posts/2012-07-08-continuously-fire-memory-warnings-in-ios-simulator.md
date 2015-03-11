---
layout: post
title: "Continuously Fire Memory Warnings in iOS Simulator"
date: 2012-07-08 23:06
comments: true
categories: 
- iOS dev
---

A while ago, Stamped developer [Andrew Bonventre](https://twitter.com/andybons) did an [ObjectiveSee interview](http://www.objectivesee.com/andrew.bonventre.html). My favorite tidbit:

> Create a key-binding for the “Simulate Memory Warning” menu action in the simulator, then simply hold down the key combination so that it's continuously firing while you click around your app.

I found it non-obvious how to do this since the `/Developer` directory has moved into the Xcode bundle. So here is how, for future reference:

<!-- more -->

1. **Open the directory** containing the iOS Simulator app in Finder, e.g.:
	* `$ open /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/Applications`
	* System menu → Recent Items[^1] → Move focus to iOS Simulator with arrow keys, then type `⌘ ↩`.
2. Go to the **Keyboard Shortcuts** panel:
	1. Open Keyboard settings.
	2. Switch to Keyboard Shortcuts tab, select Application Shortcuts.
	3. Tap the small + button.
3. Choose the iOS Simulator as the Application:
	1. Choose “Other…” from the Applications list. A file dialog appears.
	2. From the Finder window we opened in step 1, **drag the iOS Simulator into the file dialog’s search box.**  
	![Dragging iOS Simulator app into the file dialog](/images/in-posts/2012-07/iOS-simulator-search-box.png)
4. Set `Simulate Memory Warning` as the Menu Title and assign a shortcut, e.g. `^ ⌘ X` (better than `M` for right-handers, because you will want to keep it firing with your left hand while clicking through the app with your right hand).   
	![Menu Title and Keyboard Shortcut](/images/in-posts/2012-07/iOS-simulator-keyboard-shortcut.png)

There you are. Fire away!

-----------
[^1]: You <em>are</em> an iOS developer, right?
