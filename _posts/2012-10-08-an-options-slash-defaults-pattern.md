---
layout: post
title: "An Options/Defaults Pattern"
date: 2012-10-08 15:20
comments: true
categories: 
---
It’s been a while since my last post. I’ve been busy settling into my new life as a freelancer out of Frankfurt. I’m happy to be here, although I do miss New York a lot. I’ve been busy lately looking for clients ([drop me a line](mailto:hello@yangmeyer.de) if you require some iOS craftsmanship), and with small projects of my own.

For my [upcoming app](http://twitter.com/SnipsterApp), I recently forked two neat components and made them configurable through an options dictionary. I ended up with a nice drop-in solution for optional options (no pun intended) backed up by default values.

What I wanted
-------

* With [KNSemiModalViewController](https://github.com/kentnguyen/KNSemiModalViewController), view controllers can present other view controllers that cover the screen only partially. I wanted to be able to tweak the animation duration, shadow, dimming, and whether a visual push-back effect should be used during the transition.

* [DerpKit](https://github.com/amazingsyco/DerpKit) allows view controllers to resize their root view when the keyboard becomes visible. Since I wanted to use this feature on my semi-modal view controller, with a view merely 88pt high, I needed to enforce a minimum height so that the semi-modal view would be pushed up, instead of covered, by the keyboard.

What I did (at first)
-------
I extended the API with some additional methods that take an `options` parameter, e.g.:

* `-[UIViewController derp_addKeyboardViewHandlersWithOptions:]`
* `-[UIViewController presentSemiViewController:withOptions:completion:]`

Next, I had a number of smelly, repetitive code snippets that all looked like this:

	#define kYMShadowOpacity 0.3
	- (id) shadowOpacityOptionOrDefault {
		NSDictionary *options = objc_getAssociatedObject(self, @"YMOptions"));
		return options[@"ym_ShadowOpacity"] ?: kYMShadowOpacity;
	}

Brrr… there had to be a better way.

An extensible re-usable solution
-----
Here’s the gist: Register options and defaults once, then use an “option or default” method and pass in the key you are interested in. 

Or rather, *here’s* [the gist](https://gist.github.com/3852865):

	#import <objc/runtime.h>
	
	- (void)ym_registerOptions:(NSDictionary *)options
					  defaults:(NSDictionary *)defaults
	{
		objc_setAssociatedObject(self, @"YMOptions", options, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
		objc_setAssociatedObject(self, @"YMDefaults", defaults, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	- (id)ym_optionOrDefaultForKey:(NSString*)optionKey
	{
		NSDictionary *options = objc_getAssociatedObject(self, @"YMOptions"));
		NSDictionary *defaults = objc_getAssociatedObject(self, @"YMDefaults"));
		return options[optionKey] ?: defaults[optionKey];
	}

You get a drop-in NSObject category in my [YMFoundationAdditions](https://github.com/yangmeyer/YMFoundationAdditions) repo, along with other handy stuff. They are also included in my [DerpKit](https://github.com/yangmeyer/) and [KNSemiModalViewController](https://github.com/yangmeyer/KNSemiModalViewController) forks.

Let me know what you think on [Twitter](https://twitter.com/yangmeyer.) or [App.net](https://alpha.app.net/yangmeyer). Have a great day!