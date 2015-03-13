---
layout: post
title: "Creating Apps on the Beach"
date: 2013-02-25 14:43
comments: true
categories: 
- iOS dev
- Delighted
---

<div style="float:right; margin-left:1em; width:250px; font-size:0.8em;">
<p><img src="/images/in-posts/2013-02/Kuta-Lombok-beach.jpg" align="right" alt="Beach at Kuta, Lombok" /></a></p>
<p>Beach at Kuta, Lombok</p>
</div>

I spent last week on the south coast of Lombok, Indonesia. I intended to consciously take some time off from programming and, more generally, work, and so I left my MacBook Air behind and took with me only my iPad Mini and iPhone.

But then this old idea of mine – how best to exchange contact details in a casual context like conferences, meetups, or vacation in a casual manner and, crucially, following up on those contacts – popped back into my mind, this time with one or two little ideas that made this backburner of an idea so compelling that I just had to build this. And by that I meant, straight away.

This (longish) post is about how I went about **making the app happen under substantial constraints**, and sheds a light on the various **non-coding aspects of creating an app**.

<!-- more -->

## An audacious timeframe

It’s always excruciating to envision something but then having to defer reification.[^1] Also, it would make sense to have the app ready and available from the App Store for [NSConf](http://nsconference.com), which I will be attending, for seeing how well the app puts up in practice, from both a technical and UX standpoint as well as its viability as a product.

So that was the situation. With NSConf starting in 15 days, I would need to submit the app for review in 8 days (along with an expedited-review request). And for the next 6 days, I would be stranded on these beautiful beaches without access to Xcode.[^2] Which would leave me with **just two days to actually implement the code and graphics,** including a two-leg, 19-hour flight from Jakarta to Frankfurt.

## Preparation

In effect, if I wanted this to have any chance at all, I was forced to prepare as much as possible so that I would be able to focus solely on churning out code and assets in the extremely short two-day production timeframe ahead.

Turns out there is quite some “ancillary” work that can – and perhaps, *should* – be done up front, before coding a single line of code. What follows are some of the considerations I went through.

### Defining the product

* What is the **core value** of the app, what problem does it solve?
* With what does it **compete**, and how do the competing solutions solve the problem? (This needn’t be other apps; in fact, I’d consider exchanging printed business cards the most important “competitor” in my planned app’s space.)
* How is the app **different**, how does it stand apart?

Answering these questions naturally doesn’t require Xcode, and the second question can be answered by browsing the App Store in a WiFi-enabled beach bar with a cold beer in your other hand. (Life is good.)

### Choosing a personality

Choosing a personality for the app influences and informs, among others:

* the choice of colors,
* the copy text,
* the style of visual design elements,
* the name of the app.

### Interaction design

<div style="float:right; margin-left:1em; width:400px; font-size:0.8em;">
<p><img src="/images/in-posts/2013-02/Delighted-Pen-and-Paper-Wireframe.jpg" alt="Trusted tools: pen and paper" /></p>
<p>Pen-and-paper wireframe of <em>“Delighted!”</em>, amended with implementation-oriented technical annotations</p>
</div>

* What **“things”** will the app be working with? (in my app: events and contacts)
* How are these things **organized conceptually**, and how does the user get from one to the other? (standard drilldown navigation)
* Which activities in the app should be modal, i.e. force the user to either finish the modal activity or cancel it? (creating events and adding contacts)
* What real-world **metaphors** should be used, and how? Do we need a faux leather texture to convey meaning? What about drop shadows to communicate the relationship of UI elements? (my app limits itself to subtle gradients and shadows)

I prefer, and greatly enjoy, sketching out these ideas with **pen and paper**. This leads me to a heavily annotated “app map” of screens, dialogs, and transitions, similar to what the future Xcode storyboard might come to look like.

Next I use the excellent [POP - Prototyping on Paper](https://itunes.apple.com/de/app/pop-prototyping-on-paper/id555647796?l=en&mt=8) iPhone app to turn my paper sketches into an interactive prototype.[^3] The resulting tappable prototype lets me validate some hypotheses (like whether a modal-style transition makes sense for a given activity) and generally gives me – and others – a better feeling for the app.

### Market positioning

* What’s the app called? (on the home screen, on the App Store, in verbal communication, in the app’s UI itself)
*  Should the **name** be localized, and to what extent? (I’m seriously considering an fundamentally localized family of names, but am unsure about this.)
* With what **keywords** should people find the app? (+ localized keywords for other languages)
* How do you **describe** the app on the App Store (+ localizations), and verbally when talking about it?
* At what **price** do you release the app?

Most of these things you can do on the beach. Some App Store research and keyword tweaking require Internet, and depending on your language skills and localization needs, you might need a friend – or Google – for some translation help.

## Coding

Only then, after completing these marketing and concept steps, do we actually need to start coding.

At this stage, **coding means putting your distilled thoughts in terms the computer understands**, applying your craftsmanship to create a robust piece of software that is internally extensible, intelligible and perhaps even beautiful.

This is the only step in the app creation that actually requires Xcode, and the one that I hope to squeeze into the two long days (and nights), by fiat of having thought through the whole app from a technical standpoint.

### Testing

Finally, make sure that the app fulfills your criteria of quality, at the very least the user-visible aspects:

* Does the app do what it should, without crashing nor other unpleasant surprises?
* Is the app’s performance snappy?
* Is the app usable through VoiceOver, for visually-impaired users?

## Creating apps on the beach, without Xcode

Except for Coding and Testing, all of the aspects above can be worked on before hacking away in Xcode. I even believe that this **think-first approach** can lead to better apps and less iterations after having started to code. When you realize that a different approach makes more sense, it’s easier to throw overboard some quick sketches on paper than to delete a fully implemented view controller and its carefully layouted user-interface Nib.

## Status: Waiting for Review

After a long day of hacking at my parents’ place in Jakarta, a long (but productive) flight back to Europe, and an exhausting jetlagged afternoon and evening yesterday, I submitted version 1.0 of the app, which I named *“Delighted!”*

I hope that the iOS app review team approves it within the week, so that it will be available by NSConf, where I intend to use it extensively myself.

### Roadmap for the next versions

* Custom e-mail/tweet templates
* Include my vCard in followup e-mail
* Suggest current location for creating occasions (useful in the context of meeting people while travelling)
* UI localizations for German and French
* iPad/Universal version & iCloud syncing

## So… Can you develop my app in two days, too?

No.

I compressed actual coding time to just a couple of days. That was highly ambitious by any measure. But there are a number of factors that made this possible:

#### Restraint
*“Delighted!”* is a very small, simple app with a minimal feature set, no custom UI controls and containers. It does not interface with hardware components (camera, microphone, GPS, etc) nor talk to a server. The initial version will not be localized into anything but English, will not support syncing, and will not be released as a Universal/iPad app.

#### One hat
Defining product and personality, interaction design, market positioning, testing – all the non-production aspects of app creation – take time. I probably spent about 15 hours for these activities on this very simple app. Crucially, these hours were spread out over a whole week, during which the various aspects of the app evolved and ripened. So in calendar days, 15 hours of working on these important aspects of app creation quickly translate to a week or more.

*Most people underestimate the time and effort involved in going from idea to product, even when excluding coding.* I consider it *a developer’s job* to help the client understand exactly what he/she wants the app to do (this is partly [why I don’t call myself a programmer]({% post_url 2012-03-15-wanderjahre %}) but rather a developer or a craftsman). By asking the right questions, I help the client understand better the possibilities, alternatives, risks, consequences and costs of a decision at hand. Being the business guy, the concept guy, designer and coder in one person meant that I fought out contentious points with myself and could decide quickly – not necessarily better, but certainly faster.

#### Technically informed trade-offs
During the concept phase I was looking for the best solution *that would be possible to implement in a severely limited timeframe*. I was simultaneously wearing the interaction-designer hat and that of the effort-economizing developer.

This allowed to me to consciously seek out a solution that would require the least amount of code, notably by strictly limiting myself to standard controls and standard containers (like navigation controllers and presenting dialogs modally) and the styling options they come with, which requires knowledge of the available components and customization options.

#### So yes, *IF* …
* you’re planning an app with a similarly limited scope;
* we limit ourselves to standard UI components;
* forgo “fancy” features, localization, syncing, and other potentially problematic requirements;
* and, **most importantly**, spend a week together transforming your idea into a production-ready concept, by poring over the UI as well as technical details of your app —

… then *maybe* I might be able to do the coding proper in just a few days.

### Your own app?

I plan to create another app or two after this one throughout the spring and summer. That said, if you want my assistance for own app which you think might interest me, get in touch at [hello@yangmeyer.de](mailto:hello@yangmeyer.de) and I’ll see what I can do.

I’d love to hear your thoughts on this post. Shoot me an e-mail, or tweet me: I’m [@yangmeyer](http://twitter.com/yangmeyer) on Twitter and App.net. Or say hello at NSConf – I’d be delighted to meet you.

-------------------------------------

[^1]: Obviously, I’m not talking about the same thing as Langston Hughes in his poem <a href="http://www.poetryfoundation.org/poem/175884">Harlem</a>, where he asks “What happens to a dream deferred?”, but I do find his evocative imagery fitting: “Does it dry up / like a raisin in the sun?”
[^2]: Admittedly, there’s worse.
[^3]: Unfortunately the app currently requires an initial registration, which for me meant an extra trip to the nearest WiFi hotspot just to start using the app. <ins datetime="2013-03-11 15:00Z"><strong>2013-03-11:</strong> Good news – according to the release notes, this is no longer necessary as of version 1.2.0, which was released just before I left for the beach.</ins>
