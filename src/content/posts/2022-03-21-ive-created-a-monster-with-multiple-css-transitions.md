---
template: blog-post
title: I've created a monster! (...with multiple CSS transitions)
slug: /ive-created-a-monster
date: 2022-03-20 20:02
description: An article on using multiple CSS transitions.
featuredImage: /assets/picture.jpg
---
Today, instead of doing anything productive, I'm creating a TERRIFYING monster, using a grotesque amalgamation of all of the most *weird* and *arcane* of CSS properties. I am, of course, talking about...
Dun dun dunnnnn...
Transitions.

I have a bit of a confession to make. I've never done anything all that complex with CSS transitions before. I was always taught that animation is best used sparingly, and as someone who gets overstimulated by most heavily animated websites, I'm inclined to agree.

Still, I've seen some seriously cool-looking buttons that use multiple, staggered transitions, and I have always wanted to know exactly how to do it. Doing a few tutorials is fun and all, but I thought this time I would try making something weird. I love browsing codepen.io for the most bizarre toy projects that I can find, and I'm more than due for unleashing one of my own upon some unsuspecting developers.

I present to you...

SLORPO

![](/assets/the-blob.png)

He's a `<div>` upon whom I shall bestow life using the eldritch powers of CSS, and he's hungry...for delicious mouse pointers. Or he would be, if he were alive yet. He's looking a little stiff right now.

Transitions have been widely supported for quite a long time. The transition properties (`transition-property`, `transition-duration`, `transition-delay` and `transition-timing-function`) and the shorthand `transition` will take you pretty far with only a minimal understanding of their advanced functions. I'm still learing things like creating custom bezier curves. Still, like a lot of less-used CSS features, adding multiple transitions to an element can be situationally useful to create some interesting and unique effects.

Back when I first attempted to make use of transitions in a project, I was still learning CSS, and I wanted to create a button that both changed color and border radius when hovered over. But how could you do that without using JavaScript or some sort of complicated CSS trick requiring a 40 minute tutorial and another 50 lines of HTML? Extra instances of a CSS property in a rule always cancel earlier ones out, right?

```css
.test-button {
  transition-property: background;
  transition-property: border-radius;
}
/*Spoiler: Yes. Later properties cancel earlier properties out. That background property does not work.*/
```

Did the people who write the CSS spec not foresee anyone wanting to do this? They do make honest mistakes sometimes, as Kevin Powell discusses in [this](https://www.youtube.com/watch?v=O1Yc5akcrRo) interesting video, but that sounds like functionality that lots of developers might want.
Luckily, someone did think of it, and it did make it into the spec. All CSS transition properties can accept multiple, comma-separated arguments. A lot of beginners(myself included) often won't consider using multiple arguments within any property, but knowing your shorthands and where you can give properties more complex values can help with more than just transitions.

```css
.blob {
  transition-property: border-radius, background;
}
```

You can also do the same thing in the `transition` shorthand, like this:

```css
.the-blob-that-never-was {
  transition: border-radius 1500ms ease-in 0ms, background 1500ms ease-in 0ms;
}
```

That looks like a lot of repetition, right? Well, it *can* reduce the number of lines of CSS you need to write sometimes if you have transitions with a lot of different values, but if you need to use the same values with only minor variations multiple times, it'll actually make you repeat yourself *more* than if you had used the individual properties separately, as you can see in the above example.

That's all well and good, the important question is how can I make Slorpo get all rounded and blobby *before* he starts changing color? When you're using multiple arguments, they simply apply to transitioned properties in the order they are entered. This can be used to easily plan out complex transition sequences.

```css
.blob {
  transition-property: border-radius, background;
  transition-duration: 1500ms, 1000ms;
  transition-delay: 0ms, 500ms;
  transition-timing-function: ease-in, ease-out;
}
```

Or, if you want the same value to apply to multiple transitions, you can use a single argument in any combination of your `transition-duration`, `transition-delay` and `transition-timing-function` properties.

```css
.mouth {
  transition-property: width, height;
  transition-duration: 450ms;
}
```

At this point, Slorpo has a lot going on. His mouth opens, his eyes widen, he changes from a sickly gray rectangle to a vibrant lime green blob. Let's take a look. Watch your fingers(Pointers?).

<figure>
<iframe height="300" style="width: 100%;" scrolling="no" title="The Most Cursed CSS Transitions Tutorial Ever" src="https://codepen.io/ericbrown8787/embed/WNdveyw?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/ericbrown8787/pen/WNdveyw">
  The Most Cursed CSS Transitions Tutorial Ever</a> by Eric Brown (<a href="https://codepen.io/ericbrown8787">@ericbrown8787</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>
<figcaption style="color:whitesmoke">
IT'S ALIVE! This pen was inspired very distantly by Geoff Graham's <a href="https://codepen.io/geoffgraham/pen/dGoOMB">much less unhinged Codepen. </a>
</figcaption>
</figure>

If you're using transitions on multiple nested elements, I recommend at least keeping the transition properties inside of separate rules for organizational purposes. Or keeping both the transition properties and the properties that are being transitioned in their own rules if you're working on a more complex element. You can even put the transition-related properties for that component in their own separate linked stylesheet. Whatever you choose, keeping your components organized is important to avoid breaking things by making adjustments to your transitions. Messy stylesheets are the REAL monster.