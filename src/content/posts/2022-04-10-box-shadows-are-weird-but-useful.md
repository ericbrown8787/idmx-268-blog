---
template: blog-post
title: "Box shadows are weird...but useful. "
slug: /box-shadows-are-weird
date: 2022-04-10 17:35
description: "An article about what you can do with box shadows. "
featuredImage: /assets/susan-gold-gkaqq78gioy-unsplash.jpg
---
# Box shadows are weird...but useful.

For my final project in my first web development class, one requirement was that I needed to include an embedded video in order to demonstrate that I knew how to embed a video. At the time, as far as I knew, the box model was just **CONTENT ==> PADDING ==> BORDER ==> MARGIN**, with the box-sizing property determining which of those were included in sizing calculations. I knew that outlines existed, but I didn't know what the difference between them and borders were. At the time, the only time I had ever tried using the `outline` property for something was this rule I always had at the top of my stylesheets:

```css
/*UNCOMMENT ME TO DEBUG STUFF*/

/* * {
    outline: 1px solid red;
} */
```

Box shadows were just these magical things that projected a ghostly silhouette of whatever they were applied to onto whatever was underneath them. Cool, but definitely magical and unknowable and I should never think too hard about them.

Well, as it turns out, I ended up doing something a little more complicated than I was expecting to for that part of the project because the instructions were to style the embedded video somehow and my first thought was "Uhh. It's a video, so why not stick it in a box that looks like a film strip?"

![A meme, courtesy of: https://www.ranzey.com/generators/bart/index.html](/assets/meme.png)

Ok, all self-deprecating jokes aside, I feel pretty good about having actually figured out how to do that at that stage in learning CSS. I was able to get the effect I wanted out of it at least. This is what the final product looked like:

![How I styled that embedded video](/assets/about-me.png)

But now, almost 6 months and probably a few hundred hours worth of experience later, maybe I can improve it a bit. So let's start with some HTML.

```html
<figure class="outer">
  <div class="inner">VIDEO</div>
</figure>
```

```css
*,
*::before,
*::after {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

.outer {
  background: transparent;
  border-block: 20px dashed #333;
  box-shadow: 0px 20px #333, 0px -20px #333;
}
```

There. Done. In practice, the video would be inside that inner container. Embedding videos has some quirks that pretty much require the video to be isolated in a container div. This is an article on box shadows though, and our styles are mostly going to be applying to that outer container. 

![Step 1 of styling the film strip container](/assets/step-1.png)

So great, we've replicated my original effect. What's happening here is that the dashed border is creating the effect of perforations that show through to the background behind the element, which is why the background being transparent or inheriting the background color of the parent is important. To create the outer edge of the film strip, I used two separate box shadows, with an x-offset(the first numerical value in the box-shadow property's syntax) of zero so that they're exactly horizontally aligned with the element and y-offsets(the second numerical value) of a positive and negative value, in this case 20px. These values can be adjusted to your taste, but to keep it simple we're using the same value for the border and the shadow offset.  

It does look a little flat, and considering I used slight box shadows on a lot of elements to give the design of the rest of the page a slight 3d effect, it did look a little out of place in that regard. One cool thing about the `box-shadow` property is that you can add multiple comma-separated box shadows to an element. So let's add one. 

```css
.film-strip {
  box-shadow: 0px 20px #333, 0px -20px #333, 20px 40px 40px #333;
}
```

![Step 2 of styling the film strip container](step-2.png)

Now, if this were a real strip of film and not made of CSS, you'd be able to see some of the shadow being cast by the film strip through the perforations. It doesn't look like that's happening here though. 

So why not? 

Well, this shows us an important thing about how CSS box shadows work that might not be immediately obvious when you first start using them. **A box shadow isn't really a full three-dimensional shadow.** It's actually more like adding another layer of outline, with the offsets determining the thickness of the vertical and horizontal regions of the outline and the third numerical value(blur) determining how the rate at which it blurs as it gets closer to the center of the element. So it's just meant to two-dimensionally *simulate* a shadow, and the effect starts falling apart when you try to add a third dimension to it. Stephanie Eckles has a great [article](https://moderncss.dev/the-3-css-methods-for-adding-element-borders/) on how borders, box shadows and outlines work. 

So what can we do to make this look a little more aesthetically harmonious? Well, what we have here are essentially three different tools for drawing lines around boxes. Why not add some color and a nice, blocky outline around the box in order to help it stand out a bit. Since we already have a border, maybe we can just take out the extra shadow and paint on an outline. 

```css
.film-strip{
    border-block: 20px dashed teal;
    outline: 10px solid black;
}
```

and... 

![Our film strip container with an outline applied.](/assets/step-3.png)

Oops. 

Outlines have a few limitations. They can't be set to a specific side of an element, and they can't be made to follow the shape of a `border-radius` property that you set for your element, so you're stuck with perfect rectangles and nothing else. If we remember how outlines and box shadows work, neither one contributes anything to the total dimensions of the box, and they both get painted on top of the margin, so we end up with this situation where the outline gets painted on top of the shadow we're using for the outer edge of the film strip. We can solve this problem by wrapping our figure in another container div and doing some hacky stuff with margins, but that sounds like more work when we have a `box-shadow` property that can accept more arguments right here. We can reduce the total number of extra shadows we need to add by giving a positive value to the `spread` value in `box-shadow`, which is that fourth numerical value after blur. It stretches or shrinks the shadow without blurring it more. 

```css
.film-strip {
	box-shadow: 0px 20px teal, 0px -20px teal, 0px -20px 0px 10px black,0px 20px 0px 10px black;
}
```

![Our film strip container with shadows added to create an outline](/assets/step-4.png)

Now we have a bold outline outside of our box, without the limitations of the `outline` property. With one more shadow, we end up with a more interesting [Neobrutalism-inspired container](https://hype4.academy/articles/design/neubrutalism-is-taking-over-web) for a video. 

```css
.film-strip {
	border-block: 20px dashed teal;
	box-shadow: 0px 20px teal, 0px -20px teal, 0px -20px 0px 10px black,
		0px 20px 0px 10px black, 20px 40px 0px 10px black;
}
```

![The completed film strip with another shadow applied.](/assets/step-5.png)

A lot of the time, box shadows can take a bit of fiddling with to get them to look how you want them to look, but they can be a useful tool for creating more complex styles than the `border` property allows for on its own.

Finally, here's the Codepen I created this demo in. Try adjusting some of the values to see how the shadows change. 

<iframe height="600" style="width: 100%;" scrolling="no" title="A Video Container Using Box Shadows" src="https://codepen.io/ericbrown8787/embed/bGaKKvN?default-tab=html%2Cresult&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/ericbrown8787/pen/bGaKKvN">
  Untitled</a> by Eric Brown (<a href="https://codepen.io/ericbrown8787">@ericbrown8787</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>