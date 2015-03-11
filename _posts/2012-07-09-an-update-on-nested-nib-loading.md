---
layout: post
title: "An Update on Nested Nib Loading"
date: 2012-07-09 13:42
comments: true
categories: 
- iOS dev
---
In a [post on my old company’s blog](https://blog.compeople.eu/apps/?p=142), which turned out to be decently popular among fellow iOS developers, I described a way to “embed” a view with a Nib within another Nib.

This was a year ago, and it’s [more than time](http://stackoverflow.com/questions/6357547/pluggable-custom-view-nibs-nib-in-a-nib-memory-leak-why/10934581#10934581) that the approach be brought to ARC.

<!-- more -->

<ins datetime="2013-03-10 07:00Z">**2013-03-10:** I haven’t needed nested Nibs in my last few projects, so while I know that this approach is still in use in an older client project, I wasn’t sure whether this was still the best solution. I was happy that [Markos Charatzas](http://twitter.com/qnoid) approached me at NSConference with a conceptually simpler solution:  
<span style="color:transparent">.</span>  
Nib design: Use a free-form UIView as a top-level object, set YMCalendarSheet (the custom view) as the File Owner, connect outlets from File Owner to the UIView’s subviews.  
<span style="color:transparent">.</span>  
YMCalendarSheet code: In <code>-initWithCoder:</code> do <code>[self addSubview:[[[UINib nibWithNibName:@"YMCalendarSheet" bundle:nil] instantiateWithOwner:self options:nil] objectAtIndex:0]]</code> and similarly in <code>-initWithFrame:</code>.  
<span style="color:transparent">.</span>  
This approach is conceptually simpler (no need to know and understand the somewhat obscure `-awakeAfterUsingCoder:`), and it obviates the error-prone step of copying over all relevant properties from the “real” object to the “placeholder” object.  
<span style="color:transparent">.</span>  
If you’re still interested in learning about how you can use `-awakeAfterUsingCoder:` to similar effect, read on.
</ins>

Recap
------
Say we have a custom view `YMCalendarSheet`. This is what we would need to do:

1. Create a Nib (an “Interface Builder document” from the file templates) with your `YMCalendarSheet` as the root object, and hook up the outlets.
2. In some other Nib (say, your view controller’s), add a UIView, resize/position it, and change its class in the Identity inspector to `YMCalendarSheet`. This is the placeholder.
3. Implement the magic `-[NSObject awakeAfterUsingCoder:]` method in `YMCalendarSheet` to use the view from your custom Nib instead of the placeholder.

For a more detailed (and more pedagogical I guess) look into the steps, I recommend reading the [original article from last year](https://blog.compeople.eu/apps/?p=142).

ARC
------
The `-awakeAfterUsingCoder:` implementation switches out the placeholder for the object loaded from its Nib. Here is how it looked initially, pre-ARC:

	- (id) awakeAfterUsingCoder:(NSCoder*)aDecoder {
	    BOOL isJustAPlaceholder = ([[self subviews] count] == 0);
	    if (isJustAPlaceholder) {
	        YMCalendarSheet* theRealThing = [[self class] loadFromNib];
			
	        theRealThing.frame = self.frame;	// ... (pass through selected properties)
			
			[self release];				// problem
			[theRealThing retain];		//   #2
			
			self = theRealThing;		// problem #1
	        return theRealThing;
	    }
	    return self;
	}

Now, when we want to convert our project to ARC, the compiler complains about two things:

### Not allowed to assign self
Under ARC, only the [“init family”](http://clang.llvm.org/docs/AutomaticReferenceCounting.html#family.semantics.init) may assign to self. (Which *is* a sensible rule… in addition to being necessary for ARC to understand when to retain/release.)

It turns out that we don’t even *need* to assign self to the replacement object. Returning it is enough. => Problem solved.

### Not allowed to manually retain/release
The second problem is trickier, because if we don’t retain the real object (the one we just loaded from the custom view’s Nib) before releasing it, it gets deallocated and we get `EXC_BAD_ADRESS` crashes.

My ex-colleague Niels found a nifty way to trick ARC into accepting our manual memory management. ARC does not know how to handle  Core Foundation objects, so we just cast our objects and do the manual retain/release:

			CFRelease((__bridge const void*)self);
			CFRetain((__bridge const void*)theRealThing);

Problem solved.

<ins datetime="2013-03-10 07:00Z">**2013-03-10:** Apparently with newer versions of the SDK (e.g. the one shipping with Xcode 4.5) this bit of ARC trickery is no longer necessary.</ins>

Auto Layout
-----------

<ins datetime="2013-07-23 16:00Z">**2013-07-23:** Benjamin McCloskey points out that to use this pattern with Auto Layout, you need to set `translatesAutoresizingMaskIntoConstraints=NO`. Thanks, Benjamin!</ins>

He adds:
<blockquote>
If you don't turn off translatesAutoResizingMaskIntoConstraints it can cause your layout to be incorrect as well as causing a LOT of debugging nonsense in the console.

It's also worth noting that this works just the same way with Storyboards as it does with regular .xibs (i.e. you can create a UI Element in a .xib and drop it into a StoryBoard View controller by following your steps.)
</blockquote>

ARC- and Auto Layout-compatible version
------

So here it is in whole:

	- (id) awakeAfterUsingCoder:(NSCoder*)aDecoder {
	    BOOL isJustAPlaceholder = ([[self subviews] count] == 0);
	    if (isJustAPlaceholder) {
	        YMCalendarSheet* theRealThing = [[self class] loadFromNib];
			
	        theRealThing.frame = self.frame;	// ... (pass through selected properties)
			
			// Update 2013-07-23: make compatible with Auto Layout
			self.translatesAutoresizingMaskIntoConstraints = NO;
        	theRealThing.translatesAutoresizingMaskIntoConstraints = NO;

			// convince ARC that we're legit -- Update 2013-03-10: unnecessary since at least Xcode 4.5
			CFRelease((__bridge const void*)self);
			CFRetain((__bridge const void*)theRealThing);
			
	        return theRealThing;
	    }
	    return self;
	}

Demo
------
I released a [simple YMCalendarSheet component](https://github.com/yangmeyer/YMCalendarSheet) on Github to serve as an example.

One other consideration
------
Under Nib-in-Nib loading, the `-awakeFromNib` implementation will be called for both the placeholder and the real thing. Avoid side effects!
