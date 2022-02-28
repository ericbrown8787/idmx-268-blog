---
template: blog-post
title: Three modern CSS features for streamlining your stylesheets.
slug: /three-modern-css-features
date: 2022-02-27 18:50
description: An article discussing CSS custom properties, the hsl() function and
  the @prefers-color-scheme media query.
---
## Introduction

CSS has a bit of a reputation for being bloated, difficult to visualize and difficult to create concise and reusable code in. However, over the past few years, several features have been added to CSS that seek to address this problem. Here, I'd like to discuss three newer CSS features that are well-loved by developers and well-supported by browsers. 

---

## Using CSS custom properties to manage unruly CSS.

When I started learning CSS, my stylesheets were massive and disorganized. For the final project in my first web development class at my local community college, I decided very late that I would need to completely change my project's color scheme. I originally wanted to go with a pretty wild magenta and teal color scheme, but the readability was terrible and at the time I was even less of a designer than I am now, so I decided to pick an easier color palette. When it came to implementing it, it was a mess, and since I hadn't really committed to any consistent use of color, the new color scheme didn't work one-to-one with the old one. Getting the new color scheme just right took me all weekend.

Up until fairly recently, reducing repetition and implementing logic in your CSS was a tricky game of planning, assigning lots of different classes to the same element and(often) leaning on a CSS preprocessor like SASS to help maintain order.

Custom properties allow you to re-use values and combine default CSS properties in order to apply more structured logic to the way your CSS styles are applied to elements. The ability to declare a variable containing a value and then use that variable as the argument in multiple properties makes it possible create and enforce and connections between different properties of different elements, all while not requiring JavaScript or a preprocessor to implement.

For example, in flexbox and grid, the `gap` property creates space between elements inside the flex or grid container, but not around the outside of the container. In order to maintain aesthetically pleasing even spacing, I like to use the `padding` property on the container to add the same amount of space between the contents and the edges of the container as I do between the individual elements that make up the container's contents. Without custom properties, the CSS would look like this:

```css
.container {
  gap: 1.5rem;
  padding: 1.5rem;
}
```

However, since we know that we want these two properties to always be the same value, we can use a locally scoped custom property in order to tie them together, like this:

```css
.container {
  --grid-spacing: 1.5rem;
  gap: var(--grid-spacing);
  padding: var(--grid-spacing);
}
```

Because our `--grid-spacing` property was declared inside the rule applying to that specific `.container` class,the property can be used elsewhere with a different value without affecting this instance of it.

The [Open Props](https://open-props.style/) CSS library by Adam Argyle is made up entirely of CSS custom properties.

---

## Why use `hsl()`?

The HSL color space is mapped to a circular area similar to the color wheel you may have been taught in elementary school. In order to use the HSL color space in CSS, the `hsl()` function can be used to define a color.

The **hue** of your color is an angle, with the point of reference(0 Degrees) being the top of the circle at red. Being an angle, the hue must be defined in units of `deg`, `rad`, `grad` or `turn`. Being circular, the HSL color space wraps around, with positive hue values(`90deg`) interpreted as counting around clockwise and negative values(`-100deg`) counting backwards counterclockwise from 360. So a value of `-100deg` is the same as 360-100, or `260deg`. **Saturation** and **Lightness** are simple sliding scales ranging from 0% to 100%. The **Alpha** or opacity is an optional fourth argument representing the opacity of the color, and can be expressed as a number between `0` and `1` or as a percent value between `0%` and `100%`

I think a particular advantage that HSL has is that it's very easy visualize. Coming up with colors on the fly using RGB or Hexadecimal notation involves thinking through how light mixes, and once you have a base color that you like, it's easy to lose it again when you're making adjustments. It can be done with practice, but fine control over specific qualities of the color is difficult. On the other hand HSL colors have three(or four, if you count the Alpha channel) linear axes that can be modified individually to make fine adjustments to one characteristic of your color without changing the others. Adjusting the hue is a matter of sparing a thought for our old friend Roy G. Biv.

These axes can be used to great effect with CSS custom properties. Because CSS variables can be included in a property's value in any combination, with all arguments contained in one variable or split up into individual variables for each argument, it is very easy to use HSL to generate palettes consisting of multiple shades with the same base hue and saturation.

As an example, let's take a look at these five divs: 

```html
<div class="color1">Meow.</div>
<div class="color2">Meow?</div>
<div class="color3">Meow!</div>
<div class="color4">¿Meow?</div>
<div class="color5">¡Meow!</div>
```

For our shades, I assigned a base color value which contains the first two arguments in the `hsl()` function: the *hue* and *saturation*. This gives us a base <span style="color:hsl(150 100% 55%);">green</span> color to work with.

```css
:root {
  --color: 150 100%;
}
```

Next, I defined five lightness values as percentages, each 15% apart.

```css
:root {
  --color: 150 100%;
  --lightness-1: 85%;
  --lightness-2: 70%;
  --lightness-3: 55%;
  --lightness-4: 40%;
  --lightness-5: 25%;
}
```

I combined the base hue/saturation value with each of the five lightness values:

```css
.color1 {
  background-color: hsl(var(--color) var(--lightness-1));
}

.color2 {
  background-color: hsl(var(--color) var(--lightness-2));
}

.color3 {
  background-color: hsl(var(--color) var(--lightness-3));
}

.color4 {
  background-color: hsl(var(--color) var(--lightness-4));
}

.color5 {
  background-color: hsl(var(--color) var(--lightness-5));
}
```

Which, applied to five divs, gives us this:

![Five shades of the same hue using hsl()](/assets/hsl-shades.png)

\*Note: According to [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/hsl()), CSS Colors Level 4 spec changed how the `hsl()` function works in CSS. Before, in order to modify the alpha channel of the a color, the `hsla()` function would need to be used instead, but now both functions accept the same arguments and are functionally identical, so any style that previously worked using `hsla()` can now instead use the `hsl()` function.

---

## Dark and Light Theming using the `@prefers-color-scheme` media query

I'm an avid user of dark mode. Bright lights hurt my eyes. The first thing I do when I start using any app is see if it has an option for dark mode. My browser is set to dark mode. My phone is set to dark mode. I keep my fingers crossed for cloudy days to set the world to dark mode.

When making multiple themes available wasn't as common, websites that offered them typically used one of two approaches:

* The theme could be changed in a user's account settings, tying the setting to their login.
* The theme could be changed using a checkbox, button or slider somewhere on the page.

Now, however, there is a third option: the `@prefers-color-scheme` media query, which can be used to check a user's browser settings for a light mode prefference and apply the correct theme to the page accordingly.

To avoid the need to rewrite all of your color-related properties, the `@prefers color scheme` practically requires that you use CSS custom properties. `@prefers-color-scheme` accepts one of two values: `light` or `dark`. Most apps default to light mode and more users either actively prefer light mode or just stick with the default color scheme, so I think making your default styles light mode is a safe bet.

Here's a simple color scheme I created using custom properties:

```css
//light mode color scheme

:root {
  --header-color: #444;
  --article-text-color: #222;
  --page-background: lightblue;
  --box-background: floralwhite;
  --box-border: 5px solid olivedrab;
}
```

...which, applied to a simple page, gives us this:

![My simple theme in light mode. ](/assets/light-mode.png)

In order to implement a dark mode, the `prefers-color-scheme` media query can then be used to create an alternate rule that only applies when the user has set their browser's preferred color scheme to dark:

```css
//dark mode color scheme

@media (prefers-color-scheme: dark) {
  :root {
    --header-color: #ddd;
    --article-text-color: #ccc;
    --page-background: gray;
    --box-background: #333;
    --box-border: 5px solid lightblue;
  }
}
```

...which then makes the same page look like this:

![](/assets/dark-mode.png)

There are a few different schools of thought that go into picking colors for a dark theme, which I won't explain here, but this approach can be adapted to almost any CSS structure. Using Custom Properties in cases like this can help us re-use as much of our CSS for both light and dark modes as possible and keeps media queries short, specific and easy to read and adjust.