---
layout: post
title: "Best Practice: Handling Empty Table Views"
date: 2013-05-11 16:40
comments: true
categories: 
- iOS-dev
---

For a long time in my 3,5 years of iOS development, I enjoyed devising clever ways to DRY code, abstract away boilerplate code, make components more reusable, make “client” code more beautiful:

* [chaining UIView animations](https://github.com/yangmeyer/CPAnimationSequence)
* implementing [options with fall-back defaults](http://blog.yangmeyer.de/blog/2012/10/08/an-options-slash-defaults-pattern)
* using functional operators on collections (map, select, fold)

Occasionally, this entailed **making use of the impressively powerful Objective-C runtime**. Associated objects, swizzling out method implementations – exciting stuff! These sometimes-ugly details were hidden away in a separate reusable class or category. Once these components were written, the code that used them became so wonderfully concise.

I now feel that some of these solutions, while beautiful from an academic standpoint, were in fact over-engineered. In practice, I have come to **favor more straightforward approaches**.

In this post, I describe how to display a **placeholder for a table view when it is empty**. I lay out the “naive” implementation and a “clever” alternative, and finally my current best practice.

<!-- more -->

## Naive implementation: “if”s

The most straightforward implementation is to sprinkle `if` and ternary conditional statements everywhere:

    - (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
    {
        NSUInteger objectCount = [self.fetchedResultsController.sections[section] numberOfObjects];
        if (objectCount > 0) {
            return objectCount;
        } else {
            return 1;
        }
    }
    
    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
    {
        if ([tableView numberOfRowsInSection:0] > 0) {
            YMContactCell *cell = [tableView dequeueReusableCellWithIdentifier:@"ContactCell"
                                                                forIndexPath:indexPath];
            [self configureCell:cell atIndexPath:indexPath];
            return cell;
        } else {
            YMPlaceholderCell *cell = [tableView dequeueReusableCellWithIdentifier:@"PlaceholderCell"
                                                                    forIndexPath:indexPath];
            return cell;
        }
    }
    
    - (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
    {
        return ([tableView numberOfRowsInSection:0] > 0
               ? 44.0
               : 88.0);
    }

This is how I wrote my table view controllers the first couple of years. In fact, I believe that the main **advantage** of this approach is its **understandability**: Everybody *gets* it right away, because everybody [has done it](http://stackoverflow.com/questions/5225726/a-placeholder-text-to-tableview-if-the-datasource-is-empty/5225804#5225804) this way before.

The main **problem** with this approach is **repetition**. The fact that we want to treat the “empty” case differently is spread throughout the class, which obfuscates the intention and makes the code prone to bugs.

## “Clever” alternative: Magic category

While researching for this blog post, I came across a [UITableView category called NXEmptyView](https://github.com/nxtbgthng/UITableView-NXEmptyView). It’s a plug-and-play category, i.e. the promise is to drop the category files in your project, set the `nxEV_emptyView` property to your placeholder view, and the category magically takes care of displaying it at the right time.

The implementation makes clever use of some **advanced Obj-C runtime tricks**. It holds on to the custom placeholder view as an [associated object](http://oleb.net/blog/2011/05/faking-ivars-in-objc-categories-with-associative-references/) (because categories normally don’t support adding instance variables), and it uses [method swizzling](http://stackoverflow.com/questions/5339276/what-are-the-dangers-of-method-swizzling-in-objective-c) to replace `UITableView`’s original `-reloadData` and `-layoutSubviews` methods with a custom implementation.

Had I found this category two years ago, I would have admired the clever implementation and appreciated the expressiveness of the API in the client code. (As well as the craftsmanship apparent in the clear concise code and the laudable presence of unit tests.)

These days, however, I see a problem in the overly clever ways in which the category is implemented. In other words, the problem is that it’s *too* “magical”.

The **appeal** of the NXEmptyView category is its **super-simple usage** – import it, specify the placeholder view, done. The **disadvantage** is that the way it is implemented under the hoods mean that it is **hard to understand** (e.g. need to understand associated objects). The pros and cons of this clever solution are the mirror image of those of the naive solution!

## Best-practice: Separate classes for special cases

Nowadays I use a technique that I heard about in a podcast (it might have been Saul Mora<del datetime="2013-05-19 12:30:00Z">’s interviewee</del> in an episode of [NSBrief](http://nsbrief.com)). The idea is to have a **separate class for the “empty” case**, and to **set it as the table view’s datasource** when it’s empty.

<ins datetime="2013-05-19 12:30:00Z">[Saul Mora](https://twitter.com/casademora/status/335408764045893632) pointed me to [his interview with Gus “Acorn” Mueller](http://nsbrief.com/71-gus-mueller/) – dive in at the 24" mark. Thanks!</ins>

Here are excerpts from two classes right out my latest networking/followup app, [“Delighted!”](https://itunes.apple.com/de/app/delighted!-nice-to-meet-you!/id607995866?l=en&mt=8), pruned for clarity (error handling and finer-grained `NSFetchedResultsControllerDelegate` content change callbacks). This is the simple-as-bread empty datasource implementation:

    @implementation YMOccasionEmptyDataSource
    
    - (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
    {
        return 1;
    }
    
    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
    {
        return [tableView dequeueReusableCellWithIdentifier:@"EmptyContactsCell"
                                            forIndexPath:indexPath];
    }
    
    @end

And here is the view controller. Note how an `YMOccasionEmptyDataSource` instance is created, and how it is returned as the `-currentDataSource` if the fetched results come back empty.

    @interface YMOccasionViewController ()
    @property (strong, nonatomic) NSFetchedResultsController *fetchedResultsController;
    @property (strong, nonatomic) YMOccasionLoadingDataSource *loadingDataSource;
    @property (strong, nonatomic) YMOccasionEmptyDataSource *emptyDataSource;
    @end
    
    @implementation YMOccasionViewController
    
    - (void)viewDidLoad
    {
        [super viewDidLoad];
        self.loadingDataSource = [YMOccasionLoadingDataSource new];
        self.emptyDataSource = [YMOccasionEmptyDataSource new];
    }
    
    #pragma mark - Datasource switcher
    
    - (id<UITableViewDataSource>)currentDataSource
    {
        return ([self.fetchedResultsController.sections[section] numberOfObjects] > 0
                ? self
                : self.emptyDataSource);
    }
    
    #pragma mark - UITableViewDataSource
    
    - (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
    {
        return [self.fetchedResultsController.sections[section] numberOfObjects];
    }
    
    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
    {
        YMContactCell *cell = [tableView dequeueReusableCellWithIdentifier:@"ContactCell"
                                                            forIndexPath:indexPath];
        [self configureCell:cell atIndexPath:indexPath];
        return cell;
    }
    
    #pragma mark NSFetchedResultsControllerDelegate
        
    - (void)controllerDidChangeContent:(NSFetchedResultsController *)controller
    {
        self.tableView.dataSource = [self currentDataSource];
        [self.tableView reloadData];
    }
    
    #pragma mark - Fetched results controller
    
    - (NSFetchedResultsController *)fetchedResultsController
    {
        if (_fetchedResultsController == nil) {
            NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
            // ... configure fetchRequest ...
            _fetchedResultsController = [[NSFetchedResultsController alloc] initWithFetchRequest:fetchRequest
                                                                            managedObjectContext:self.managedObjectContext
                                                                            sectionNameKeyPath:nil
                                                                                    cacheName:self.occasion.title];
            _fetchedResultsController.delegate = self;
            
            // perform initial fetch. display loading-indicator cell while performing fetch.
            self.tableView.dataSource = self.loadingDataSource;
            [self.tableView reloadData];
            dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
                [_fetchedResultsController performFetch:nil];
                dispatch_async(dispatch_get_main_queue(), ^{
                    self.tableView.dataSource = [self currentDataSource];
                    [self.tableView reloadData];
                });
            });
        }
        return _fetchedResultsController;
    }
    
    #pragma mark - Configure cell
    
    - (void)configureCell:(YMContactCell *)cell atIndexPath:(NSIndexPath *)indexPath
    {
        // ...
    }
    
    @end

You might have noticed how I use a second “placeholder” datasource for when the table view is loading (`YMOccasionLoadingDataSource`). In effect, with this pattern you can have a **separate data source implementation for each special case.**

My best-practice solution is **decidedly more code** than the clever solution’s one-liner, and therefore **seems** more complicated.

However, it is **conceptually much simpler**. There is no magic happening behind the curtains, but just a simple matter of pointing the table view to a special datasource for a special case.

As always, let me know your thoughts. I’m [@yangmeyer](http://twitter.com/yangmeyer) on Twitter and [App.net](http://alpha.app.net/yangmeyer).
