---
layout: post
title: "Navigate like a Captain"
date: 2012-07-17 12:15
comments: true
categories: 
- iOS dev
---
Last week, I started work on a location-based iPhone app that will revolutionize how people discover cities. A major pain point was simulating “incremental” location changes, i.e. simulating movement.

So I hacked together an AppleScript app that allows you to “navigate” to the North, South-West, or South-East. It’s [available on GitHub](https://github.com/yangmeyer/YMDevScripts).

<!-- more -->
Usage
------
This is how it looks:

![Simulate movement in the iOS Simulator](/images/in-posts/2012-07/iOS-simulated-navigation.png)

Instructions
------
1. Open script in AppleScript Editor, save as Application.
2. Make the Application [invokable through a keyboard shortcut](http://superuser.com/questions/245711/starting-application-with-custom-keyboard-shortcut), e.g. <del>⇧⌘L</del><ins datetime="2012-07-18T16:06:00EST" class="inline">`^L`</ins> [^1].
3. (For demo purposes:) In iOS Simulator launch Maps app.
4. Hit <del>⇧⌘L</del><ins datetime="2012-07-18T16:06:00EST" class="inline">`^L`</ins> and choose a direction, and see the blue location indicator move by about 600 meters.

Limitations
------
* I am by no means proficient in AppleScript, and rather happy I got it to work at all! So don’t expect nice code on this one.
* Only N, SW, and SE bearings possible. This is because dialogs in AppleScript have a maximum of three buttons. (These three bearings allow moving around the map through linear combinations.)
* If you don’t like what you see, but like the idea – hey, it’s open-source, so fork away, and I’ll be glad to merge your pull requests.

Get it from [the GitHub repository.](https://github.com/yangmeyer/YMDevScripts)

---------------
#### Updates
[^1]:⇧⌘L is a bad shortcut for the iOS Simulator. I just discovered – after months of this happening seemingly at random! – that pressing ⇧⌘ simultaneously Toggles Slow Animations.
