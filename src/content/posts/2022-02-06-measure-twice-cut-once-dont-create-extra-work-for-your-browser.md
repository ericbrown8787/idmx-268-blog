---
template: blog-post
title: "Measure Twice, Cut Once: Don't create extra work for your browser. "
slug: /measure-twice-cut-once
date: 2022-02-06 18:21
description: "An article that discusses performance considerations in web development. "
---
## Introduction

Before I ever started learning programming or web development, I picked up building computers as a hobby in my mid-20s when my budget laptop started failing me and I realized that being done with school(at least for the moment), portability was no longer a concern. I used my personal computer in exactly one spot in my house, I didn't need it for work, and in the rare event that I travel anywhere I barely even leave my phone on, much less lug around a laptop. As I got more familiar with computer hardware, I started paying more attention to performance metrics. Part of it was me trying to get the most out of my hand-picked components, but I also found it interesting to just watch what parts of my computer started working harder when I was browsing the web, or playing a game, or if I felt like living dangerously...opening an Excel spreadsheet.

Now, as a programming and web development student with a level of control over the things that affect a computer's performance that I didn't have before, I watch my system's performance even more closely than ever. Browsers are interesting in terms of performance. There's a running joke spanning multiple PC building subreddits about needing high-performance hardware to run Chrome. Before, I had always thought of web browsers as being pretty compact applications that magically work on just about any device. However, depending on exactly what you're doing with it, a web browser can consume a massive portion of your system's resources performing seemingly simple tasks. 

Web browsers themselves are applications, and their own performance overhead may not seem like much, but their purpose is to be the environment inside which other third-party applications are run. Because of this, their effect on their host device's performance is largely up to us as web developers to optimize.

## What Happens When A Browser Loads A Page?

[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/Performance/How_browsers_work) has a great article that describes the process of populating a page from DNS lookup to your browser spilling pixels forth onto your display. For out purposes here, I want to divide this process into two major steps:

1. The browser obtains the necessary resources from the server and parses them
   to generate a DOM tree.
2. The browser renders the page with all of its components to the screen based on the calculations it made in step 1.

Both of those steps themselves have numerous steps to them, and both processes run simultaneously, but I think it helps to imagine these as two good targets for some basic, high-level optimization practices. Today I'd like to focus on the rendering step to discuss what we as developers can do to write better code while keeping the rendering process in mind.

## Optimizing Page Rendering

When CSS or Javascript acts to change the appearance of elements on a webpage, the browser follows a series of steps to translate those changes in instructions into what appears on a user's display.

![The steps of rendering a page in a web browser](/assets/steps-of-page-rendering.jpg)

[Image source: developers.google.com](https://developers.google.com/web/fundamentals/performance/rendering)

One of my first experiences with these concepts happened while I was working on a JavaScript practice project, in which I used a for loop to append a number of divs(determined by squaring a number input by the user) into a grid container to create a two-dimensional canvas that the user could draw on by moving their pointer over the container.

```
const gridContainer = document.getElementById("gridContainer");

for (let i = 0; i < squaresPerSide * squaresPerSide; i++) {
    let gridBox = document.createElement("div");
    ...
    gridContainer.appendChild(gridBox);}
```

![a screenshot of my project](/assets/etch-a-sketch-cropped.png)

Originally, I had my loop appending each div directly to the inside of the container div on on every iteration. I tried generating a small 16x16 grid. It seemed a little slow.

I tried 32x32. Much slower. That's weird. Huh.

I tried 100x100, and my browser froze.

I was confused. None of the other submitted projects I looked at seemed to have this problem. At the time, I didn't understand how costly layout calculations can be, but after doing a little research and some testing, I understood that every iteration of my loop was causing my browser to recalculate the page's layout, meaning I had effectively implemented a "do a bunch of pointless math" button in my etch-a-sketch project. So appending 256 cells one at a time to my container div was slow. Appending 1024 cells was slower. Appending 10,000 cells was enough to overwhelm my system.

In order to reduce these unnecessary recalculations, I figured out that I could append my divs to a DocumentFragment object instead.

```
const gridContainer = document.getElementById("gridContainer");
const gridContents = document.createDocumentFragment();

for (let i = 0; i < squaresPerSide * squaresPerSide; i++) {
    let gridBox = document.createElement("div");
    ...
    gridContents.appendChild(gridBox);}

    gridcontainer.appendChild(gridContents);
```

The DocumentFragment exists outside of the DOM tree, and by itself is not rendered by the browser, meaning that I could have my loop generate each individual box in my grid inside of a temporary container, and then append the contents of that container to the real container that would be rendered in my browser all at once, reducing thousands of layout recalculations down to one.

Now, you're not writing loops with thousands of iterations in CSS, but these lessons still apply to operations triggered by CSS properties. I made my JavaScript mistake on a mid-to-high-end desktop PC with a discrete GPU, but for an app used by the general public, a significant portion of your users are going to be on mobile devices.

A **reflow** is an operation that causes the browser to re-render the layout of a page. During a reflow, the browser recalculates the position and dimensions of some or all of the elements in the document.

A **repaint** is an operation that rasterizes elements, converting calculated values into a map of pixels that appear on your display. While repaints also incur a significant performace cost, they do not affect the position or dimensions of the element that they are acting on or surrounding elements. Rasterization, even at the best of times, requires a lot of processing power to calculate once, let alone multiple times in the event of re-rendering.

As a general rule, I think a developer concerned with making their site usable on a wide variety of devices should be mindful of how elements are rendered by the browser and what conditions can trigger a reflow or a repaint.

As a page renders, the browser may recalculate the layout several times, but there are a few steps that can be taken in order to reduce the total number and complexity of layout calculations. Being sure to set dimensions for images can help, as the browser can allocate space for an image before it is loaded instead of recalculating after the fact. Using more modern layout models such as Flexbox over older layout methods such as floats can also help.

### Animations

Animations in particular, while important to creating a good user experience when used correctly, can be costly in terms of system resources, and sticking to animating CSS properties that only trigger the **composite** and (with caution)**painting** phases of the rendering process whenever possible can help improve performance. When a CSS property alters the geometry of an element and triggers a reflow, the entire page's layout may need to be recalculated to accommodate the target element's new geometry. Colt Steele has a [video](https://www.youtube.com/watch?v=N5EW4HnF6FU) that explains this topic in detail.

[CSS Triggers](https://csstriggers.com) is a useful site that provides a list of CSS properties and what parts of the page rendering process they trigger in different browser engines.

When we're positioning elements in CSS, we might use properties like `top` or `left` to get an element to appear exactly where we want it. However, as we can see in CSS Triggers, using these positioning properties intended for laying out the page before rendering in an animation causes the browser to recalculate your element's geometry, restarting the rendering process from the layout phase.

Unfortunately, the fact that most CSS properties trigger a recalculation of their target element's geometry means that they can have a noticeable effect on performance when animated. However, as Colt Steele explains, there are a few versatile properties that do now incur these performance costs.

**Transforms** are your friends when implementing animations, as in most browsers, transforms don't trigger reflows, and the transform property is very versatile, moving and distorting elements without recalculating their fundamental layout. Using `transform(translate)` moves elements around the page while only triggering a new composite operation. **Opacity** works quite well for a similar reason. While it does trigger a repaint, this repaint is confined to the element that the opacity property is acting on, and doesn't trigger a recalculation of the entire layout.

## Conclusion

While it can be difficult or impossible to avoid *all* reflows and repaints, it is important to keep the operations that trigger them in mind when building a webpage, especially when it comes to creating websites that will perform well on mobile devices. Animation is an important area to watch, as animating the wrong properties can effectively undo most of the work that a user's browser did when initially rendering a page, and if a page performs poorly on a desktop or laptop, chances are the problems are going to be even worse on mobile.

As with most topics related to web development, the best approach to optimizing your project for performance depends very much on your project's specicfic use-cases, but having some understanding of how browsers use the resources of the device that they are running on can help us as developers create applications that use these resources efficiently to create the best user experience.