---
layout: post
title: "International App Store Woes"
date: 2012-07-04 15:39
comments: true
categories: 
- iOS dev
---

In [Four Years of App Store](http://www.macstories.net/stories/four-years-of-app-store-developers-weigh-in-on-search-discovery-and-curation/), Federico Viticci summarizes the key pain points of developers with the App Store. There is, however, one aspect from the user’s perspective that he does not address: For international users, there are some extra problems that arise because of the different App Stores.

<!-- more -->

Curtailed Choice
----
There are quite a few apps that I would like to use but are not sold on Germany’s App Store, or have limited functionality.

It’s not that I don’t understand the decisions and the motivations underlying them.

- I understand the self-censorship of [Timeline World War 2](http://timelineww2.com/), the acclaimed educational app, to avoid problems with Germany’s [law banning the depiction of swastikas](https://en.wikipedia.org/wiki/Strafgesetzbuch_%C2%A7_86a).
- I understand why [Starbucks](http://itunes.apple.com/us/app/starbucks/id331177714?mt=8) thought it should omit the popular “Mobile Pay” pay-by-QR-code feature from the German app. Phased rollout perhaps, and country-bound credit cards.
- I understand why [Dark Sky](http://itunes.apple.com/us/app/dark-sky/id517329357?mt=8), the amazing weather app that was kickstarted last year, is not sold in Germany. The weather data is only available for the US.
- I understand why [Yardsale](http://itunes.apple.com/us/app/yardsale/id454934979?mt=8) might have wanted to build up a bustling community in the US first, without exposing themselves to the risk of users elsewhere leaving 1-star reviews complaining “absolutely useless!!! no users in my city!”

**But.** What if I spend a summer in the States and *want* to learn about [Coventry](https://en.wikipedia.org/wiki/Coventry_Blitz), *want* to buy coffee at Starbucks without cash, *want* to know when it will start raining here in NYC, *want* to engage in exchanging my belongings super-locally here in Greenpoint.

The current setup is just bad for people who travel or have more than one home base.

Language
----
I for one have set my device to English, as I have always done with all my devices. (I find that in general the English localization uses less screen space – compared to, say, German – and often matches the user interface metaphor better.)

Since I am browsing the German App Store, I see the German-localized description and screenshots. Even though I have my locale set to English.

Workaround
-----
The common workaround goes something like this:
1. Buy an iTunes gift card for the country you are visiting.
2. Create an Apple ID without a credit card (payment type: None).
3. [Redeem the gift card](http://support.apple.com/kb/ht1574)
4. If necessary, [change your App Store country](http://support.apple.com/kb/ht1311).

This makes [some things more tedious](http://apple.stackexchange.com/questions/36217/different-itunes-region-and-app-updates). You will need to add the second account to your iPhone/iPad. To update apps you got through that account (the other store), you need to switch to that account first.

It’s a hard problem
-----
As much as I would love to ask Apple to “just fix it already!”, I realize it’s actually a hard problem to solve. Just some considerations off the top of my head:

### Locale settings
Should the App Store respect my locale setting for the description and screenshots? This might make sense *if*, say, the Starbucks app in the US and German stores are actually *identical* (have the same App ID). Otherwise, I might see the QR-code-payment screenshot and would wrongfully assume that this feature was supported in Germany.

### Different reasons for differentiation
App Store differentiation has different motivations, e.g. legal (Timeline WW2), community-building (Yardsale), data (Dark Sky), infrastructure rollout (Starbucks?). There is no *one* way for Apple to solve all these issues.

For legally motivated differentiations, I guess there is not much leeway at all.

For all the other reasons, one conceivable option is for Apple to let the developers check a box to display some kind of warning to unsupported countries (e.g. “Might not work as advertised in your country. Is intended to work in: USA, Canada.”). On the buyer side, there could be a global option to hide unsupported apps, or a banner displaying the warning on the app’s description.

Not any time soon
------
Alas, all these ideas just don’t sound like Apple at all, so don’t count on any of this happening any time soon.
