---
template: blog-post
title: "Clamp down the right font size with new CSS features. "
slug: /clamp-down-the-right-font-size
date: 2022-02-13 19:33
description: "An article that introduces the CSS clamp feature and how it can be
  used for font sizing. "
featuredImage: /assets/clamp.jpg
---
### Introduction

Choosing the right font sizes for a webpage is something of a hidden art in web design. I think that adjusting typography properties is one of the most difficult parts of creating breakpoints for different screen sizes. When I create breakpoints, the part that I spend the most time trying to perfect is always the typography, particularly around that no-man's land that exists immediately before and after each breakpoint, where my typography starts looking a little off before ***WHAM***! The breakpoint kicks in and all of a sudden my font has suddenly started changing size and text starts moving around the page.

More often than not, I feel like adjusting font size for different screen sizes requires making too many small adjustments to multiple values just to get it past the "sloppy rough draft that I would be embarassed to show anyone" stage and into the "sort of coming together" stage.

When I'm sighting in my breakpoints, I often find that having the font size change from one static value to another leaves me with some awkward sizes right before and after the minimums of each breakpoint that just won't quite look right, sometimes even forcing me to subdivide typography breakpoints into smaller ranges just to eliminate these awkward zones, which of course means I have even more numbers I need to keep adjusting as I make other changes.

Now, this responsive approach using media queries is a perfectly valid way to adjust font sizes for different screens. It's widely supported, and a skilled developer can make it look good. But it's also time-consuming and adds complexity to projects, and using CSS that doesn't need to be changed as much between screen sizes can help reduce these costs.

**Fluid typography** is text that scales...well...fluidly based on the size of the user's viewport. The idea behind fluid typography is this: Rather than snapping to different sizes at breakpoints, a page's font size adjusts dynamically according within a set of limits that accomodates for different screen sizes. I'm pretty new to development, and I feel lucky in that regard. I can only imagine the hacky Javascript solutions people may have come up with to do this before. Now there's another, CSS-only way though.

### Introducing: the clamp() Function.

The new-ish **clamp()** CSS function allows you to set a property's value as a range of values using the following syntax:

`clamp(minimum, preferred, maximum)`

In addition to allowing the fluid adjustment of font sizes, this function also allows developers to combine sizing units in clever ways, and even provides a viable way to use sizing units such as pixels(px) and viewport height/width units(vh, vw) that have useful properties but are not quite ideal for setting font sizes by themselves.

According to [caniuse.com](https://caniuse.com/?search=clamp), clamp() is widely supported as of the writing of this article, with IE 11 predictably being the exception as far as major browsers go.

I'm not here to talk about the function in exhaustive technical detail. [Kevin Powell](https://www.youtube.com/watch?v=wARbgs5Fmuw) and [DesignCourse](https://www.youtube.com/watch?v=dg488RrpNTc) do a much better job of that than I can at this point. Instead, I would like to talk about what fluid typography and the options that the clamp() function opens up could mean for developers.

I think that an important advantage of using fluid typography and clamp() is that rather than thinking of font sizes as a set of static values corresponding to different screen sizes, it allows us as developers to think of font size as a mathematical *relationship* between the size of the screen and the size of the text that allows for finer adjustments to text size in relation to screen size that would be difficult or impossible using older methods.

Good web design depends heavily on being aware of relationships between design elements, and having access to what is effectively a way to represent these relationships on an adjustable sliding scale could make developing for a wide variety of devices much faster and much easier if used in the right ways. Newer CSS features like `clamp()`, `calc()` and variables help a lot with this.

### "Should I use THE CLAMPS?!"

![A generic clipart image of a robot with clamp-hands who does not violate any copyright laws. ](/assets/robot.png)

While fluid typography may have the potential to solve some problems with traditional responsive design using media queries, implementing fluid typography can also introduce its own challenges. For one, it could allow for some strange behavior when interecting with breakpoints that change properties of other aspects of the page, especially when making a change that results in the fundamental layout and geometry of the page changing.

For example, if we change to a grid layout at a certain screen size, it is possible that your initial fluid typography parameters may result in text that is too large if you use absolute viewport-bound units such as `vh` and `vw` as your minimum and maximum, as these units are tied to the dimensions of the viewport, not the container(which may have changed its width at the breakpoint).

While CSS isn't actually meant to be a full-featured programming language on its own, it is meant to be a tool for translating designs into functioning interactive web pages. With the variety of different sizes and types of devices that webpages need to be able to display on constantly getting more diverse, having tools in CSS that allow us to incorporate more complex logic into the way sites display on different devices has the potential to make designing with this in mind a lot easier.

### Why not take it further?

Adjusting font sizes dynamically is impressive, sure, but according to MDN the clamp() function can be used in any property that allows a `<length>`, `<frequency>`, `<angle>`, `<time>`, `<percentage>`, `<number>`, or `<integer>`. That's almost anywhere you can enter a numerical value in CSS. Combined with a robust organizational system for your CSS such as BEM, it may even be possible to build fully responsive pages, especially simpler landing single-page sites, with minimal media queries.