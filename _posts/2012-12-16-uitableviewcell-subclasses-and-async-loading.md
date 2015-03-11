---
layout: post
title: "UITableViewCell subclasses and async loading"
date: 2012-12-16 19:11
comments: true
categories: 
- iOS dev
---
It’s been a while since my last post. I have been busy working with some great people on an in-house iPad app for a leading German car maker.

In the process, I re-used and refined a UITableView technique that I learned from developing [Snip*](https://itunes.apple.com/us/app/id568099336), which I would like to share with you.

<!-- more -->
Table views and collection views
-------
Ever since the first iPhone OS SDK, UITableView has been a staple ingredient of most (non-game) apps, for compelling reasons:

- its vertically scrolling UI works well for displaying a list of things on an iPhone screen;
- its clever API enforces a separation of datasource and behavioral configuration code;
- its implementation allows for smooth scrolling even for very long lists by recycling cells.

With the iPad and its larger screen, the vertical list was no longer the only sensible solution to displaying a large number of things (in cells). Open-source components like [AQGridView](https://github.com/AlanQuatermain/AQGridView) offered a two-dimensional grid, adopting UITableView’s proven datasource/delegate API as well as its cell re-use mechanism. In the iOS 6 SDK, Apple introduced UICollectionView, which brings with it all the generic functionality of handling a number of cells (notably datasource/delegate callbacks and cell re-use). Collection view cells can laid out following a vertical, horizontal, circular, or really any arbitrary pattern – this pattern is described in a separate layout class. In effect, UICollectionView is a more powerful, more flexible incarnation of UITableView.

Cell re-use
-------------
In both UITableView and UICollectionView, cells are “re-used”. For a table view of a thousand cells that displays ten rows (cells) at a time, only those 10 rows – plus a small number of surrounding rows – are created and held in memory.

When the user scrolls down, the cells that are pushed out of view (e.g. rows 1 and 2) are recycled and re-used for any new cells that will come into view (e.g. rows 13 and 14). This is *much* more efficient than creating all of the thousand cells.

In other words, cells are configured lazily, i.e. not upfront but only when they are needed. This is a great approach if we can access the information for the cell quickly.

Here is an example from my latest iPhone app, [Snip*](https://itunes.apple.com/us/app/id568099336), that worked well enough for simple text snippet cells:

	- (UITableViewCell *)tableView:(UITableView *)tv cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
		YMTextSnippet *snippet = [self snippetAtIndex:indexPath.row];
		UITableViewCell *cell = [tv dequeueReusableCellWithIdentifier:@"YMTextSnippetCell" forIndexPath:indexPath];
		cell.titleLabel.text = snippet.textContent; // fast!
	    return cell;
	}

Asynchronous loading
---------
Often, though, we first need to load (from the file system or a web server) or calculate the information that we want to display in the cell, e.g. an image from the file system, or a graph that we first need to plot from statistical data.

If we simply do the expensive calculations in the `-tableView:cellForRowAtIndexPath:` callback, we will get some jerky scrolling – not at all the smooth, natural feel we should be aiming for.

The solution is moving the calculations away from the main thread. We can do this easily with [Grand Central Dispatch](https://developer.apple.com/library/ios/#documentation/Performance/Reference/GCD_libdispatch_Ref/Reference/reference.html) (GCD) by wrapping the expensive calculations in a `dispatch_async(…)` block, and then updating the table view back on the main thread.

In the case of Snip*, it was photo and voice snippet cells that took longer to load. I created three UITableViewCell subclasses: YMTextSnippetCell, YMImageSnippetCell, YMVoiceSnippetCell.

Here is a tentative asynchronous implementation:

	- (UITableViewCell *)tableView:(UITableView *)tv cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
		YMSnippet *snippet = [self snippetAtIndex:indexPath.row];
		NSString *cellIdentifier = [snippet.entity.name stringByAppendingString:@"Cell"];
		YMSnippetCell *cell = [tv dequeueReusableCellWithIdentifier:cellIdentifier
													   forIndexPath:indexPath];
		
		if ([cell isKindOfClass:[YMTextSnippetCell class]]) {
			cell.titleLabel.text = ((YMTextSnippet*)snippet).textContent; // fast
		}
		else if ([cell isKindOfClass:[YMImageSnippetCell class]]) {
			dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
				UIImage *image = [(YMImageSnippet*)snippet imageFromFileSystem]; // expensive !
				dispatch_async(dispatch_get_main_queue(), ^{
					((YMImageSnippetCell*)cell).imageView.image = image;
				});
			});
		}
		else if ([cell isKindOfClass:[YMVoiceSnippetCell class]]) {
			dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
				id recording = ((YMVoiceSnippet*)snippet)recording; // expensive !
				dispatch_async(dispatch_get_main_queue(), ^{
					((YMVoiceSnippetCell*)cell).voiceRecording = recording;
				});
			});
		}
		
		return cell;
	}

This works – most of the time.

Closure caveat
------
If you scroll too fast, you will see flickering cells: the wrong cells are updated when the aynchronous calculation finishes. This is because the Objective-C block we pass into  `dispatch_get_main_queue` is a closure and therefore remembers the pointer to `cell` as it was when the block was passed in. After scrolling hard, the cell might have been re-used for a different index path – and *bam!* we are updating the wrong cell.

Session 211 of WWDC ’12 offers a good explanation, along with a solution. Instead of accessing `cell` directly, in the `dispatch_get_main_queue` block, ask the table view for the cell at the current index path:

	dispatch_async(dispatch_get_main_queue(), ^{
		UITableViewCell *currentCell = [tv cellForRowAtIndexPath:indexPath];
		((YMImageSnippetCell*)currentCell).imageView.image = image;
	});

(As a side note, since I was surprised to hear of app developers who don’t know about them: make sure to check out the [WWDC videos](https://developer.apple.com/videos/), which are a trove of invaluable tutorials, explanations, and tips, straight from Apple’s engineers.)

Now it works – but we’re not done yet. The convoluted datasource implementation is not a very nice solution:

- having a big conditional for each of the snippet types feels clumsy;
- casting the snippets and their cells is ugly;
- having the controller know/import all specific cell subclasses seems unnecessary;
- repeating the GCD async dance sucks.

Let the cell display the model
-------
Instead of micromanaging every detail of the cells’ contents in the datasource callback, I prefer to devolve responsibility for displaying a “thing” to the cell itself. In the case of Snip*, I introduced YMSnippetCell, an abstract superclass for the snippet cells:

	@class YMSnippetCell;
	typedef YMSnippetCell* (^YMOriginalCellBlock)(void);
	
	@interface YMSnippetCell : UITableViewCell
	@property (nonatomic, strong) YMSnippet* snippet;
	- (void)setSnippet:(YMSnippet *)snippet asyncUpdateOriginalCell:(YMOriginalCellBlock)currentCell;
	@end

The datasource implementation is now much simpler, and the controller does not need to know about the concrete cell subclasses:

	- (UITableViewCell *)tableView:(UITableView *)tv cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{
		YMSnippet *snippet = [self snippetAtIndex:indexPath.row];
		NSString *cellIdentifier = [snippet.entity.name stringByAppendingString:@"Cell"];
		
	    YMSnippetCell *cell = [tv dequeueReusableCellWithIdentifier:cellIdentifier forIndexPath:indexPath];
		[cell setSnippet:snippet asyncUpdateOriginalCell:^{
			return (id) [tv cellForRowAtIndexPath:indexPath];
		}];
	    return cell;
	}

And this is how the asynchronous image-loading looks in the concrete YMImageSnippetCell class:
	
	@implementation YMImageSnippetCell
	- (void)setSnippet:(YMImageSnippet *)snippet
	{
		NSAssert(NO, @"Use -setSnippet:asyncUpdateOriginalCell:!");
	}
	- (void)setSnippet:(YMImageSnippet *)snippet asyncUpdateOriginalCell:(YMOriginalCellBlock)currentCell
	{
		NSAssert([snippet isKindOfClass:[YMImageSnippet class]], @"Snippet type mismatch.");
		[super setSnippet:snippet];
		self.imageView.image = nil; // reset
		dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
			UIImage *image = [snippet imageFromFileSystem]; // expensive
			// we could also rotate + scale image
			dispatch_async(dispatch_get_main_queue(), ^{
				YMImageSnippetCell* originalCell = (id) currentCell();
				originalCell.imageView.image = image; // update UI
			});
		});
	}
	@end

To recap, we pass the model to the cell and ask it to configure its views accordingly. To work around the “closure caveat” that we discussed before, we pass in an “original-cell accessor block”, which the cell uses for updating its UI on the main-thread.

Thoughts on MVC
-------
As a side note, be aware that this approach deviates from the [MVC pattern](https://developer.apple.com/library/ios/#documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html) that Apple champions and, to an extent, enforces through UIKit. The other day I followed an interesting [conversation on App.net between @mzarra, @ctp, @rtmfd, @rix and @kirbyt](https://alpha.app.net/kirbyt/post/1860085#1860021) about just this subject, discussing the merits of subclassing UITableViewCell vs using `-viewWithTag:`. (I was shocked to learn that the venerable Marcus S. Zarra prefers `-viewWithTag:` as a lightweight solution… *brrrr!*)

In strict MVC, the view should simply display stuff, and it is the controller’s responsibility to choose and transform the model’s data for display, and passing the prepared data to the view. As we have seen, this approach can leads to bloated `-tableView:cellAtIndexPath:` datasource implementations with considerable code smells.

Therefore I prefer passing in the model object, but take care to use it exclusively in a read-only mode, and to never change the model from the view.

Overkill?
------
In my current client project, I extracted the GCD dance even further, encapsulating it in the abstract superclass. The superclass defines hooks for its subclasses, e.g. for the reset, expensive loading, and UI updating parts.

It’s a nice exercise and cleanly separates generic and specific code. But the fact of having a mini-framework just for custom cells feels a bit over-engineered. What do you think?

I’m [@yangmeyer on Twitter](http://twitter.com/yangmeyer) and [App.net](http://alpha.app.net/yangmeyer).