---
title: "My iPad coding wishlist for 2025"
date: 2025-01-30T22:00:00-00:00
draft: true
tags: ["Playgrounds", "Swift", "iPad"]
categories: ["Workflow"]
---

# The story so far

Last year, I developed and released [Medlied](https://apps.apple.com/app/id1606367519) from my iPad using Swift Playgrounds. I [cut out the Mac from my workflow completely]({{< relref "2024-10-12 App development on iPad" >}}), enjoyed live on-device previews that update in seconds, and I even got the privilege of doing a [talk](https://www.youtube.com/watch?v=OHJFmmDuztU&pp=ygUdd2FpdCwgeW91IGNhbiBjb2RlIG9uIGFuIGlwYWQ%3D) on my experience using Swift Playgrounds at Leeds Mobile!

Unfortunately, this setup still has some holes which I ran into with my next project.

First, Swift Playgrounds support for iOS 18 has failed to materialise. With the Swift Student Challenge only four days away, Apple has left iPad users without a way to try out the iOS 18 SDKs. Consequently, SwiftPM projects edited in Xcode may no longer compile when reopened in Playgrounds, and [due to Swift limitations](https://forums.swift.org/t/do-we-need-something-like-if-available/40349), which have gone unaddressed on the basis that everyone using Swift uses Xcode, no amount of conditional compilation can actually work around the issue.

Second, for [Shimmer Browser](https://apps.apple.com/app/id6739163018), I knew I wanted it to be free to use, with a full unlock option. As Swift Playgrounds does not offer the in-app purchase capability flag (or maybe it does, but I'd be guessing at undocumented SwiftPM syntax), I couldn't continue building it purely in Swift Playgrounds.

Reluctantly, I've dug out my MacBook Air again and used it [to power my iPad's external display](https://community.folivora.ai/t/ipados-menu-bar-theme-my-setup-request/40845?u=salvagedtechnic) for Xcode. Having breakpoints available without quickly changing editor is handy, but I miss the power of the M4 on my iPad, and the ability to have live previews of the full running app in 1-2 seconds, rather than waiting for my M1 Air to built the app, then another 30 seconds for the debugger to connect and allow the app to run!

I described a way to add local packages to a Swift Playgrounds app in my [previous blog post]({{< relref "2024-10-12 App development on iPad" >}}). It's possible to upload a package built in this way to Github, and import the library in Xcode. I would have used this to move everything back to a Swift Playgrounds app by now, with 3 (!) local packages for each of the app, widget, and shared code, but doing so now would also mean stripping out iOS 18 functionality, which I make use of in a few places to support iOS 18's widget tinting feature properly. I'll be relieved to move most of my editing back to iPad when the time comes!

So, I've put together a quick wishlist for what I want to see come to the iPad, either via the long-overdue Playgrounds update… Or the secret Xcode for iPad project that Apple will never mention until they release it unexpectedly, following in the footsteps of Final Cut and Logic.

# iPad coding - first aid to make things work

* iOS 18 support (duh)
* A way to take screenshots for the App Store, as you can't upload without this. I don't mind if the answer is Apple sending me a new iPad and iPhone each year.

# iPad coding - for professionals

* In-app purchases, subscriptions, and all the monetisation capabilities
* Xcode branding and/or a commitment to actually updating whatever the leading iPad IDE is
* Bug fixes
* Build targets & testing support (can technically be worked around with previews)
* No seriously, ongoing regular bug fix updates
* Breakpoints
* Crash reports
* Or even just stack traces for crashes… Oh wait, this used to work, but it needs a bug fix!
* Localisation support
* Accessibility inspector
* Performance graphs, memory graphs, and instruments more widely

# iPad coding - nice-to-haves

* Local app installation - can use Testflight as a workaround
* Built-in git integration
* Xcode Cloud
* Private repo dependencies, for private code shared across multiple projects
* Direct Package.swift editing

# iPad coding - oh if you must

* Apple Intelligence

# See you in 2026!

If I can check off even just the first half dozen points on this list this year, I'll be happily iPad-free for all my personal projects, not just IAP-free ones!