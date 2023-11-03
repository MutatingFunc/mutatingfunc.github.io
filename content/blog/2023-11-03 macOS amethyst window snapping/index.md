---
title: "Fixing macOS windowing"
date: 2023-11-03T22:31:00-00:00
draft: false
tags: ["iPad", "Mac", "Amethyst"]
categories: ["Workflow"]
---

In my [previous post]({{< relref "2023-10-31 iPadOS vs macOS" >}}) I discussed how our prefer a few aspects of iPadOS over macOS. In this post, I'll be discussing how to fix the big one - macOS's window positioning.

## The problem

The fundamental means of positioning windows in macOS has never really received a rethink since the inception of windowing in the early days of the OS. It's designed for screens with big pixels, and as pixels have gotten smaller it's become increasingly fiddly to keep windows from being an untidy mess.

{{< video src="macOS multiwindow" title="Arranging windows on macOS Sonoma" >}}

![The problem](macOS%20-%20the%20problem.png)

This situation has lead to the creation of a whole array of third-party window managers, usually designed around use of keyboard shortcuts to position windows in various regions of the screen. I don't get on with these - I prefer direct interaction. Workflows where new windows are being created usually involve the mouse, and swapping to the keyboard and back each time is an extra step.

## Third-party solutions

There's a few window managers I've tried which can be controlled via mouse.
* Swish, which allows swipes on a mouse/trackpad while pointing at a titlebar to position windows to halves and quarters, but you still have to reach for keyboard modifiers to achieve any sort of flexibility beyond that.
* BetterSnapTool, which allows creation of drop regions for positioning windows, but these are fragile and break when changing display or resolution.

Swish is probably the window manager I've had the most success with, but I've never been able to shake the feeling that I'm fighting macOS, deliberately avoiding the built-in window positioning & resizing interactions. I always have to fall back to them at some point, especially now I'm using Stage Manager where Spotlight support for grouping windows is still missing on macOS.

### Amethyst

---

{{< youtube yM03W1vWj28 >}}

---

There's another category of window manager - automatic tiling window managers. This is the only kind of solution which doesn't present an extra step every time you open a window, instead attempting to improve on default window behaviour.

[Amethyst](https://github.com/ianyh/Amethyst/releases) is one example - windows are automatically arranged according to your choice of a wide range of layout rules. These include a large center window with columns either side, a left-hand primary window with a stack on the right, and even a hands-off "Floating" window layout which disables tiling entirely. It even lets you pull standalone windows out to ‘float’ them, and you can drag windows between ‘slots’ and even resize them, all using the native window interactions. It's free, and open source, so anyone can add more layouts!

Unfortunately, tiling windows has a few drawbacks. Windows can't overlap - with professional apps, overlap is often useful, or even necessary, on smaller screens. Layouts often break down as windows hit their minimum size and start to break free from the layout, and you're back to being in conflict with the native windowing system.

Enter iPadOS 16 and Stage Manager…

## iPadOS window management

{{< video src="iPadOS multiwindow" title="Arranging windows on iPadOS 17" >}}

Using Stage Manager on iPadOS is a breath of fresh air. No longer do I have to fiddle with pixel-perfect window positions or sizes, because here windows snap to an invisible grid when other windows are nearby. Yet I still have total flexibility to arrange windows how I like, because _I never needed this interaction to be pixel-perfect_ - having things neatly align is more valuable.

The interaction is a callback to the context in which pixel-perfect windowing was meant to work, back when everyone used 800×600 screens, with big, visible pixels. Freeform windowing was easy then, pixels were big visible squares and windows aligned to the pixel grid, so even our clumsy hands could manage a neat arrangement.

By introducing an alignment grid, iPadOS has captured the context that freeform windows were designed around, that has been lost as displays improved. And now, once again, arranging windows can be something neat and orderly, intuitively playful.

---

## Fixing macOS windowing

So now we've hit upon a solution, right? If windows on macOS could snap to a grid too, maybe we could finally go from fighting the native interactions of the platform, to embracing them. Arranging windows on the fly would be a breeze!

So Apple will bring this to macOS right?

Another year. Another macOS release. Still, we're craning our necks, gripping our mice for precision to try and perfectly align two windows, because macOS can't be intuitive, playful, neat, and orderly like iPadOS.

People use it for work. It has to be this way.

And that's that.

…

…

…

Or is it?

Is there some crazy idea we missed?

…

If Amethyst layouts can tile some windows according to a preset layout while allowing others to float, doesn't that mean they're arranging windows in a way which supports overlap?

And if dragging or resizing a window can be a trigger for Amethyst to reflow the layout, doesn't that mean it can fully integrate with native interactions?

So couldn't a custom-made layout act like the Amethyst's freeform "Floating" layout, but just nudge windows ever so slightly so that they always align with an invisible, ~64pt grid?

{{< video src="macOS+Amethyst multiwindow 3-way" title="Creating window layouts on the fly is a breeze" >}}

Yes, yes it could. And it's glorious.

{{< video src="macOS+Amethyst multiwindow" title="macOS with a helping hand from Amethyst" >}}
