# Techniques for Creating Textured Text 

In this article we’re going to explore several techniques that can be used to
create textured text or apply a background to text.

*Please remember that some of the techniques covered in the article are 
experimental with very low browser support, and may not be the best technique to 
use when you’re building real projects that need wide browser support.*

I’ve included a screenshot for each demo, the link to the demo in the end of
each technique. You can also click the screenshot to see the respective demo.

## Applying an Image Background to Text Using `-webkit-background-clip: text`

We’re going to start with the CSS `background-clip` property and use it to get 
the following result:

![textured-text-demo#1][textured text]

The CSS [`background-clip` property][1] determines an element’s **background
painting area**, which is the area within which the background is painted. By
default, the background extends all the way to the border of an element with a
default value of `border-box`, and it can take other values like `padding-box`
and `content-box`, which are self-explanatory.

Different effects, such as [giving an element transparent borders][2], can be
created using this property, and it is pretty [well supported][3] in all modern
browsers.

The `background-clip` property [was extended in Webkit][4] with a fourth value,
`text`, which causes the background image to **clip to foreground text**
(including decorations and shadows). Then, by giving the text a transparent
color using the Webkit-only property `-webkit-text-fill-color`, the background
image will show through the text, thus completing the clipping effect.

The `text` value of the `background-clip` property is, at the time of writing of
this article, **not part of any standard yet, so unfortunately it can only work
in WebKit browsers**, and a simple CSS fallback can be provided for other
browsers, or [a polyfill][5] could be used to provide other fallbacks.

For now, we’ll create a text with a background using the `-webkit-background-
clip` property, and provide a CSS-only fallback which will [show the image
underneath the text][6] for non-WebKit browsers.

For our demo (shown in the screenshot above), we have an element with a
background and a headline inside it, and we want to clip its background to make
it appear as if the background is only applied to the text inside it.
   
    <div class="container">
        <!-- the element whose background we're going to clip -->
        <div class="clipped">
            <h1>CO<br/>OK</h1>
        </div>
        <!-- arbitrary content -->
        <p><span>The #1 cooking magazine in the world.</span> New healthy and delicious recipes every week. Subscribe to the weekly issue of COOK magazine and stay up-to-date on the latest kitchen trends and tips anc tricks from the world's #1 chefs. <br/>
        Download our app available for Android, iOS and Windows phones.</p>
    </div>

We’re going to use a “fat font”, so that the background is more visible through
the text. The `.clipped` element will get a background image, which we’ll clip
to the text using the `text` value of the `background-clip` property. We’re
going to set the text color to white, because non-WebKit browsers will display
the text on top of the image and we need to pick a font color that would be
readable on the background we chose. Then we’re going to set the text’s fill
color to transparent, which will override the white text color in WebKit
browsers so that the background image can show through.
    
    .clipped {
        background: url(../img/kitchen.jpg) no-repeat center center;
        background-size: cover;
        color: #fff;
        text-align: center;
        padding: 2em;
        /* -webkit-background-clip clips the background of the element to the text */
        -webkit-text-fill-color: transparent; /* overrides the white text color in webkit browsers */
        -webkit-background-clip: text;
    }

**Check out demo 1: [Text with Background using `background-clip:text`][7]**

If you’re viewing the demo in a non-WebKit browser, you will see a white text on
top of a background image.

Displaying the background image behind the opaque text in non-WebKit browsers is
probably the best fallback for when the image plays an important role in
conveying a visual message, but you can also fall back to regular text without
the image in the background, if you want. Divya Manian has written [an excellent
article][8] explaining how to achieve that regular text fallback, and Rachel
Nabors has created a Sass mixin based on Divya’s code and has made it [available
on Codepen][9] for you to use if you want, too.

Now, just like we applied an image background to our text, you could also apply
a gradient background to it. Because gradients in CSS are images you can apply a
gradient to the `.clipped` element above, and the result will be a text with a
gradient background. Riza Selçuk Saydam has created a very nice [interactive
step-by-step demo on Codepen][10] which explains how a text gradient is made.
You can check it out for a more visual explanation of this technique.

## Applying Texture to Text Using `mask-image: url();`

The following CSS technique we’re going to go over is, at this time, also
supported in WebKit browsers only (see [support tables here][11]) with the
`-webkit` prefix.

We’re going to use **CSS masks** to create a nice textured text effect. [CSS
Masking is a W3C working draft][12], and so hopefully it won’t be long before
other browsers also start implementing it.

But for now, we’re going to create a demo that will currently work in WebKit
browsers only, and we’ll provide a simple fallback for non-supporting browsers.
We’ll be using CSS’s [`mask-image` property][13] to apply a splatter effect to
text to get the following result:

![textured-text-demo#2][text with splatter effect]

**When we’re using CSS masks, we’re making the text take the shape of its mask 
image, instead of making the image take the shape of the text as with the 
`background-clip` technique above.**

The `mask-image` property takes one of three values: an image with alpha
opacity, where the transparent areas will be the areas where no content will
show, a CSS gradient, or `none`. For our demo, we’ll be using an image with
alpha opacity, which looks like the following:

![splatter-mask_1-codrops][an image with alpha opacity]

The image represents a bunch of paint splatters that we want to apply to the
text to make it look like it’s got some watercolor paint on it. Non-supporting
browsers will simply display the text without the splatter effect applied to it.

When you apply this mask to your text, or to any other content, the text will be
visible where the black splatters are, and the parts where the mask image is
transparent the text will not show. You can imagine your text (or content) as a
layer and the mask as another layer, and then imagine placing the mask image on
top of the text, and then get an eraser and start erasing all the parts of the
text that are not covered by those black spots, then you’ll end up with the
masked text as shown in the demo’s screenshot above.

To make the effect more realistic, I added a watercolor background to the
element containing that text, which will show through the text where it has been
“erased”, therefore nicely blending the text with its background. All of this,
using CSS only. Pretty neat, right? Too bad browser support is still very low at
the moment. But we’ll go over recreating this effect to work cross-browser in
the next section.

Now, for our demo, we have a container wrapping two headings:
    
    <div class="container>
      <h1>Art</h1>
      <h2>Digital Magazine</h2>
    </div>

We’ll be applying the splatter effect to the `h1` element. Here’s the relevant
CSS for our effect:
  
    .container h1 {
        font: 35em/100% "Oswald", sans-serif;
        margin: 0 auto;
        padding: 0;
        text-align: center;
        color: #fff;
        /* the property that creates the splatter effect */
        -webkit-mask-image: url(../img/splatter-mask_1.png);
        mask-image: url(../img/splatter-mask_1.png);
    }

And that’s all you need to achieve that effect. Now, of course you’ll want to
add some extra styling like I did in the demo, depending on the overall effect
you’re after, but that one line of CSS is what’s important when you use a mask
to add texture to text.

One important thing to note here is that when this property is implemented in
other browsers, a `-webkit-` prefix may or may not be the only prefix you need.
You need to add the `-webkit-` prefix for this to work at the time of writing of
this article, and if you do use this technique in a project, make sure to update
your code whenever the spec or browser support changes.

**Check out demo 2: [Textured Text using `mask-image`][14].**

The demo will fall back to a regular text over an image in non-supporting
browsers.

You can read more about CSS Masking in the [W3C spec][15], and check another
example of subtle textured text in [this post][16] by Trent Walton.

## Creating textured text with SVG

Both of the previous effects can be recreated using SVG, and the best thing
about it is that the results obtained with SVG are cross-browser, so you can use
the SVG method as a fallback for non-supporting browsers for the above CSS
properties.

### Clipping A Background to Text using An SVG `<clippath>`

First, we’re going to recreate the CSS `background-clip: text` effect using
SVG’s `<clippath>` element.

![demo#3][text with clipped background]

A [clipping path][17] determines the shape or region where the background is
going to be applied. The parts of the background that lie outside the defined
shape will not be painted, and only those inside the given shape will be.

A clipping path can take several values, one of which is a `<text>` element.
When the clip path is set to a text element, the background will be clipped to
the shape of the text used.

Once a clipping path is defined (we’ll get to the code in a minute), that path
can be referenced using `clip-path`, either as an attribute of the image that we
want to clip, or as a CSS property of that image.

Let’s start by defining the clip path that we want to use to clip our image,
which in our case will be just a single word that we want our background image
to show through.

First we create an `SVG` element which will contain all our SVG code. Then we’ll
define our clip path and apply it to an `image` element via the `clip-path`
attribute:
    
    <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1200" height="400" viewBox="0 0 1200 400">
        <!-- add title for accessibility -->
        <title>Adding Background to Text using SVG clipPath</title>
        <defs> 
          <clippath id="my-path">
            <text x="50" y="300" font-size="200" >WATERCOLOR</text>
          </clippath>
        </defs>
        <image xlink:href="img/watercolor_1.jpg" clip-path="url(#test)" width="100%" height="100%" preserveAspectRatio="none" />
    </svg>

*Because an SVG element is basically a graphic, make sure to include a title so 
that it’s accessible by screen readers.*

In the code above, we’ve created a `text` element, given it a font size, and
defined the x and y coordinates which determine where the text will be drawn
inside our SVG. Then, we’ve placed our text element inside a `clippath` element,
which we’ve given an ID so that we’d be able to reference it. Then, we placed
the `clippath` element inside a `defs` element. The `defs` element can be used
to store content that will not be directly displayed. This stored hidden content
can then be referenced and displayed by other SVG elements, which makes it ideal
for things such as patterns that contain reusable graphics. And in our case,
it’s perfect for *defining* our clip path that we want to reference in our image
attribute.

Next, we added our image, gave it a width and a height, and set the
`preserveAspectRatio` to `none` so that its aspect ratio does not get locked,
because if it does, then the height and width won’t be applied to it if they
don’t preserve its aspect ratio.

And last, we referenced the clipping path we defined by using the `clip-path`
attribute on the image. The `clip-path` attribute takes the ID of the `clippath`
element as its value, and applies the clipping path to the image.

**Check out demo 3: [Text background using SVG `<clippath>`][18].**

Instead of applying the clip path to the image by referencing it with the `clip-
path` attribute, you could also apply it via CSS like so:
   
    image {
        clip-path: url(#my-path);
    }

This should work as expected in all modern browsers: Chrome, Safari, Opera,
Firefox and IE9+. So, if you do want to use the CSS `background-clip: text;`
property you could, for example, check for browser support for `background-clip:
text` using [Modernizr][19], and provide the SVG as a fallback for non-
supporting browsers, and this is kind of what Tim Pietrusky’s [polyfill][20]
does, but instead of using a background image and clipping it using `clippath`,
it uses an SVG pattern to fill the text with the background image that you would
initially want to clip. In the next section, we’re going to go over filling a
text with a pattern in SVG.

### Applying a Background To Text By Filling It With An SVG `<pattern>`

So another way we could apply a background or pattern to text is by *filling*
the text with a pattern, instead of using a background image and clipping it to
the text. For this demo, we’re going to fill our text with a wooden pattern
which I got from [SubtlePatterns][21].

![demo#4][text filled with a wooden pattern]

Using an image and clipping it to text could be a great choice for when you’re
using decorative fonts like we did in our first demo, where we wanted a big
portion of the image to show through the text, because the image plays a role in
defining the character of our demo. But sometimes, all we want to do is just add
a simple and subtle pattern to text to give it that extra nice touch which makes
it less dull. In that case, using SVG, we can define a pattern just like we
defined a clipping path in the previous section, and then we can use that
pattern to *fill* the text with it.

Similar to the previous demo, we have an SVG element with a title added for
accessibility, a `text` element which we want to fill with the pattern, and a
`defs` element which we’re going to use to *define* our pattern. The pattern
consists of an `image` and has an ID that we’ll be using when we want to
reference it and apply it to our text. We’re going to reference the pattern
inside the text’s `fill` attribute, or via the text’s CSS `fill` property.
    
    <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="700" height="400" >
        <!-- add title for accessibility -->
        <title>Applying a patterned background to text in SVG</title> -->
        <!-- Source: http://lea.verou.me/2012/05/text-masking-the-standards-way/ -->
        <defs>
            <pattern id="filler" patternUnits="userSpaceOnUse" width="400" height="400" >
                <image xlink:href="img/purty_wood.png" width="1200" height="600" preserveAspectRatio="none" />
            </pattern>
        </defs>
        <text x="100" y="70%" font-size="200" fill="url(#filler)">WOOD</text>
    </svg>

And similar to the previous demo, you could reference the pattern via CSS like 
so:
    
    text {
        fill: url(#filler);
    }

**See demo 4, which also works in all modern browsers, in action: [Text 
background using SVG text fill with image][22].**

At this point, it is worth noting that not only can you use an image as an SVG
pattern, but because gradients are, as we mentioned before, also images, you can
also create an SVG gradient and use that to fill the text, instead:

![demo#5][gradient text]

Creating and using a gradient in SVG is simple. A gradient is created as a
`radialGradient` or `linearGradient` element, and is given an ID, and is then
referenced in the `fill` attribute or CSS property like we did with the
`<pattern>` above. Here’s a simple example where a gradient is used to fill the
text.

**Check out [demo 5][23] that shows text being filled with a gradient.**

And of course, a gradient can also be set as the container’s background image,
and then we can clip the background using `clippath` just like we did in the
previous section.

### Applying Texture to Text Using an SVG `<mask>`

![demo#6][watercolor text]

Just like we can apply an image mask to an element using the CSS `mask-image`
property, we can also apply the same masking concepts and get the same effects
using [SVG masks][24].

An image with opaque and transparent areas would define where the element would
be painted just like we saw in the previous section with the CSS `mask-image`
property. There is one important difference, though, between CSS image masks and
SVG image masks: **SVG masks, unlike CSS masks, use the luminance values in a
mask to determine what gets masked, not the alpha values**. So an image that
would be composed of black and transparent areas, for example, like the splatter
mask we used above, would have to be replaced with an image composed of white
and transparent areas. So, if we were to recreate the same splatter text effect
that we created above, we would have to use a mask image that looks like the
following:

![luminance-mask-codrops][luminance image mask]

An alpha channel image mask can be easily converted to a luminance image mask in
Photoshop by applying a white color overlay which turns the black areas into
white, and that is what I have done to replace the CSS image mask with this one
suitable for SVG masks. The light gray areas visible in the above image are
actually transparent, I just gave the image this background so you can see the
white splatters which otherwise wouldn’t be visible on the white background of
this post.

Now, just like we defined our SVG clipping path and pattern, we can define our
mask in a similar way and apply it to our text.

We will create an `image`, wrapped inside an SVG `mask` element, which will get
an ID so that it can be referenced, and the `mask` will be wrapped inside the
`defs` element, which is used to define our mask without directly rendering it.

So it’s like saying: we’re defining (`defs`) an SVG mask (`mask`) which is an
image (`image`) with the following URL (URL to the luminance mask above), and
then we’ll apply this mask to our text using the `mask` attribute on the `text`
element, or, of course, via CSS using the `mask` property.
  
    <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1000" height="380" >
        <!-- add title for accessibility -->
        <title>Adding Texture to Text using SVG Masks</title>
            <defs>
                <mask id="mask">
                    <image width="1200px" height="1200px" xlink:href="img/splatter-mask_luminance.png" preserveAspectRatio="none"/>
                </mask>
            </defs>
        <text x="50" y="70%" font-size="168" fill="white" mask="url(#mask)">WATERCOLOR</text>
    </svg>

And to complete the effect, I added the watercolor background to the main `svg`
element to make it look like the text is blended into that background.

**Check out the cross-browser result in [demo 6][25].**

If you want you can read more about combining CSS and SVG masks in [this
excellent article][26] by Christian Schaefer on The Nitty Gritty blog. There’s a
lot more potential to SVG masking than this, as things can get quite interesting
when you use [animated Canvas-driven backgrounds][27], for example, as masks.

## Creating textured text with HTML5 `<canvas>`

The last technique we’re going to go over uses the HTML5 `<canvas>` element to
create the above effects. With `<canvas>`, you can also “clip” an element to the
shape of another, “fill” text with texture, and apply a mask-like effect to
text, by changing the value of the `globalCompositeOperation` property of the
canvas context.

Before we get into the how of it, let’s define our markup, because it’s the same
markup for the 3 techniques we’re going to cover.

We have a canvas element for which we specify a height and a width, and before
the closing tag, we’re going to add a text content which will be available for
screen readers to read, and for browsers that have JavaScript disabled to fall
back to. One note here, though, is that Firefox does not display the text as a
fallback for the canvas when JavaScript is disabled, which [may be a bug][28].
    
    <canvas id="canvas" width=1000 height=600 >
     <!-- fallback text added for accessibility and no-js browsers -->
      WATER
    </canvas>

Now that we have our markup ready, let’s start drawing some textured text!

For our first demo, we’re going to create a text on our canvas, and **fill it**
with a pattern of our choice to get the following result:

![demo#7][water textured text]

We’re going to get our canvas element and its drawing context, and then we’re
going to create an image, and use that image as the fill style for our text.
  
    var canvas = document.getElementById("canvas");
    var ctx = canvas.getContext("2d");
    //create image we're going to use as a fill pattern
    var img = document.createElement("img");
    img.src="img/water.jpg";
    //draw the text
    img.onload= function(){
      drawText();
    }
    //function that draws the text and fills it with the texture from the img
    function drawText() {
        ctx.font = "bold 200pt Oswald";
        ctx.fillStyle = ctx.createPattern(img, 'repeat');
        ctx.textAlign = 'center';
        var x = canvas.width / 2;
        ctx.fillText("WATER", x, 400);
        ctx.fill();
    }

So, let’s go over the above code. All the drawing happens inside a function
`drawText`. We’re going to define the font style in the context’s `font`
property, and then using the context’s `createPattern` method, we’re going to
create a pattern out of the image we created before, and we’re going to set that
pattern as the value for the `fillStyle` property, which determines, of course,
the **fill style** for the font. And last but not least, we’re going to actually
draw the text on the canvas using the `fillText` method, which takes a string
which is the content of the text we want to draw, and a couple of x and y
coordinates, which, as you may have guessed, determine where on the canvas the
text will be painted.

Check out [demo 7][29] in action.

There’s one thing to note here: when you’re using a custom font inside your
canvas, you may not be able to see that custom font rendered, because the canvas
drawing may happen before the font has been loaded. To work around this, you
could, for example, use a font loader like the [Web Font Loader][30] developed
by the Typekit and Google developers, and draw on the canvas *after* the font
has been loaded:
    
    WebFontConfig = {
        google: { families: [ 'Oswald' ] },
        fontactive: function() {
            var canvas = document.getElementById("canvas");
            var ctx = canvas.getContext("2d");
            var img = document.createElement("img");
            img.src="img/water.jpg";
         
            img.onload= function(){
                drawText();
            }
 
            function drawText() {
                ctx.font = "bold 200pt Oswald";
                ctx.fillStyle = ctx.createPattern(img, 'repeat');
                ctx.textAlign = 'center';
                var x = canvas.width / 2;
                ctx.fillText("WATER", x, 400);
            }
        }
    };
    (function() {
        var wf = document.createElement('script');
        wf.src = ('https:' == document.location.protocol ? 'https' : 'http') +
         '://ajax.googleapis.com/ajax/libs/webfont/1/webfont.js';
        wf.type = 'text/javascript';
        wf.async = 'true';
        var s = document.getElementsByTagName('script')[0];
        s.parentNode.insertBefore(wf, s);
    })();

*The demo files will all use this technique but I’ll keep it simple here and 
only show you relevant function for the technique in the following examples.*

Now, moving to the next technique, we’re going to create a clipping-like effect
on our canvas by changing the value of the `globalcompositeOperation` property
of our context. The final result will look like the following:

![demo#8][text with fire texture]

The canvas context comes with the property `globalcompositeOperation` which
defines the composite operations between what is known as the “source” and the
“destination”. That is, it defines how what you want to draw will be blended
with what is already drawn on the canvas. **The source is what you want to draw,
and the destination is what is already drawn before you set the
`globalCompositeOperation` property**. We can draw new elements or shapes behind
existing shapes and mask off certain areas and even clear sections from the
canvas using this property.

The `globalCompositeOperation` can take one of 16 values, each of these values
is demonstrated clearly in a [visual example][31] on MDN, so check that out if
you’re not familiar with this property yet, as those examples will help you
understand the values we’re going to set next a lot better. Here’s a screenshot
of the results of each of those values. In the demo’s source code, the
destination is the blue rectangle which is drawn on the canvas first, and the
source is the red circle which is drawn after the `globalCompositeOperation`
value is set.

![canvas-composite][canvas globalCompositeOperation examples]

The `source-in` value of this property could be translated in plain English to
“draw only the parts/areas of the source and destination where they intersect”.
So, if you have two elements, one drawn and another one that you want to draw,
the result will be that only the areas where these two elements intersect will
be painted.

To help understand this better, let’s get into the code right away.
    
    var canvas = document.getElementById("canvas");
    var ctx = canvas.getContext("2d");
 
    var img = document.createElement("img");
    img.onload = function () {
       drawText();
    }
    img.src="img/fire.jpg";
    function drawText() {
   
        // put text on canvas
        ctx.font = "bold 180pt IM Fell Great Primer SC";
        ctx.fillStyle = 'white';
        ctx.textAlign = 'center';
        var x = canvas.width / 2;
        ctx.fillText("FIRE", x, 325);
 
        // use compositing to draw the background image only where the text has been drawn
        ctx.globalCompositeOperation = "source-in";
        ctx.drawImage(img, 0, 0, img.width, img.height, 0, 0, canvas.width, canvas.height);
    }

In the above code, inside the `drawText` function, we have 3 “sections” that
define the result of what will be drawn on the canvas. The first section is
where we define the text, including all its styles, and then we go ahead and
draw it using the `fillText` method. Next, we set the `globalCompositeOperation`
of our context to `source-in`. Then, we draw the image on the canvas.

So you see, the “source” in this case is the element that we want to draw after
the `globalCompositeOperation` has been set (the image), and the “destination”
is the text that we drew before we set it. And by setting the value to `source-
in`, we’re telling the browser to draw the image only in the areas where this
image intersects with the text, and that’s exactly how we get that clipping
effect that we’re after.

Once you wrap your head around each of the values of the
`globalCompositeOperation`, you can start using them quickly and with ease, and,
again, do make sure you check the visual examples on MDN, because they help a
lot.

When you do check them out, you’ll notice that if we set the value to `source-
atop`, the result will be also the same for our demo. `source-atop` tells the
browser to draw the parts/areas of the source (image) where it intersects with
the destination (text), and also draw the remaining areas of the destination
even those that do not intersect with the source, and in our case, that would
also result in the same effect that we want.

**View the [live demo (demo 8)][32] for this technique.**

Note: at the time of writing of this article, not all values of the global
composite operation property are supported across browsers. There are six modes
that work across browsers (in Chrome, Safari, Firefox, and Opera): source-over,
source-atop, destination-over, destination-out, lighter, and xor. You can read
more about this [here][33]. And for more advanced examples of using the global
composite operation property, check out [this post][34] on HTML5Rocks to learn
more about creating some really cool typographic effects with `canvas`.

And last but not least, we’re going to create one more effect using `canvas`,
but this time we’ll be adding some texture to the text to blend it into the
background like we did in the previous techniques with SVG and CSS. The demo
will look like the following:

![demo#9][scratched text]

We’re going to be using an image with alpha channels as our “mask”, and we want
the text to be drawn only where it intersects with the black areas of the image,
and to be “erased” where the image is transparent, right? So we want our source
and destination to only be painted where they intersect with each other.
According to this logic, and to the above screenshot of the
`globalCompositeOperation` values, we’ll want to use the `source-in` value to
get the result we’re after.

Below is the image that we’re using as a “mask”. The black areas are where the
image is going to intersect with the text. Just like we did in the CSS masking
section, you can imagine one layer for the text and another for this image, put
them on top of each other, and then erase any part of the image or text where
the two don’t intersect with each other, and you get the final effect resulting
from the `source-in` global composite operation.

![brushed-codrops][brush strokes]

The Javascript code is the same as the one above, only the value of the
`globalCompositeOperation` is different, so we’ll jump right to the live demo,
where you can check out the source code and play with it.

**See the [demo (demo 9) in action][35].**

And here again we added a background to our canvas to make the blending effect a
little more realistic. We have a “scratched” background with scratched text that
blend with each other nicely.

## Animating Text Backgrounds

Not only can text have a background, but you can actually animate this
background and create some neat interactive effects with it. Because this
article is focused on techniques for adding background/texture to text, we won’t
be going over how to animate these backgrounds, because that could be a whole
article on its own.

Mary Lou has written [an article][36] where she experimented with the CSS
`background-clip: text` property, and created some neat text background effects,
so make sure to check them out!

Trent Walton has also written [an article][37] where he goes over creating a
text with a gradient background and animates that background resulting in a
really nice and subtle effect.

And as always, [Codepen][38] includes some creative experiments using the CSS
`background-clip` property with CSS animations, and among those I chose this
[really cool experiment][39] by Lucas Bebber, in which he creates a nice effect
of a text being filled with water, and provides a fallback for browsers that
don’t support `background-clip: text`, so check that out as well and fiddle with
the code, for there are some really cool techniques used to create the filling
water effect.

## Image Replacements

Because of the lack of browser support for a lot of the techniques used to
create textured text effects, a lot of developers have been resorting to image
replacement techniques to display graphical text.

There have been a lot of techniques available throughout the years, and Chris
Coyier has done a great job collecting all of these techniques with their pros
and cons in what he called the [CSS Image Replacement Museum][40].

If you do not want to use any of the above techniques to create “graphical” text
effects, and want to use one of the image replacement techniques available, you
should also have a look at the newest of all techniques, a great technique by
Scott Kellum, the one he called “The New Kellum Method”, in which he suggests
using sort of an “empty” font, so to speak, which is a font which can be defined
with the `@font-face` rule, and whose characters have no width and no marks,
making the text invisible, and therefore allows you to avoid all the other
techniques where you would need to hide the original text to make only the
text’s background show.

There are a lot of articles out there that elaborate on these image replacement
techniques, and because these techniques are outside the scope of this tutorial
which is aimed at showing techniques to create graphical text *without* having
to replace it with an image, I’m only going to list a few resources that I
recommend reading if you want to know more about (or use) an image replacement:

* The [CSS Image Replacement Museum][41] on CSS-Tricks
* [Replacing The -9999px Hack][42] (The Kellum Method) by Jeffrey Zeldman
* [Another CSS image replacement technique][43] by Nicolas Gallagher
* [The New Kellum Method][44] by Scott Kellum

## Final Words

I hope you learned a new technique or two from this article. I think we covered
most of the possible ways to to create textured text with CSS and HTML. My
favorite technique has to be the SVG one, simply because of its wider browser
support. And when the day comes that all browsers support CSS masks and the
`background-clip`‘s `text` value, we can start using them without having to
worry too much about providing fallbacks anymore, and then CSS would definitely
be the best choice for us.

I hope you found this article useful. Thank you for reading! =)

*Do you know other techniques for adding textures to text? If you do, make sure 
you share them in the comments below!*

[1]: http://dev.w3.org/csswg/css-backgrounds/#the-background-clip
[2]: http://css-tricks.com/transparent-borders-with-background-clip/
[3]: http://caniuse.com/#search=background-clip
[4]: https://www.webkit.org/blog/164/background-clip-text/
[5]: https://github.com/TimPietrusky/background-clip-text-polyfill
[6]: http://css-tricks.com/image-under-text/
[7]: http://tympanus.net/Tutorials/TexturedText/index.html
[8]: http://nimbupani.com/using-background-clip-for-text-with-css-fallback.html
[9]: http://codepen.io/rachelnabors/pen/hacbD
[10]: http://codepen.io/rss/full/pxuzb
[11]: http://caniuse.com/#search=mask
[12]: http://www.w3.org/TR/css-masking/
[13]: http://www.w3.org/TR/css-masking/#the-mask-image
[14]: http://tympanus.net/Tutorials/TexturedText/index2.html
[15]: http://www.w3.org/TR/css-masking/
[16]: http://trentwalton.com/2011/05/19/mask-image-text/
[17]: http://www.w3.org/TR/SVG/masking.html#EstablishingANewClippingPath
[18]: http://tympanus.net/Tutorials/TexturedText/index3.html
[19]: http://modernizr.com/
[20]: https://github.com/TimPietrusky/background-clip-text-polyfill
[21]: http://subtlepatterns.com/
[22]: http://tympanus.net/Tutorials/TexturedText/index4.html
[23]: http://tympanus.net/Tutorials/TexturedText/index5.html
[24]: http://www.w3.org/TR/SVG/masking.html
[25]: http://tympanus.net/Tutorials/TexturedText/index6.html
[26]: http://thenittygritty.co/css-masking
[27]: http://updates.html5rocks.com/2012/12/Canvas-driven-background-images
[28]: https://bugzilla.mozilla.org/show_bug.cgi?id=707806
[29]: http://tympanus.net/Tutorials/TexturedText/index7.html
[30]: https://developers.google.com/fonts/docs/webfont_loader
[31]: https://developer.mozilla.org/samples/canvas-tutorial/6_1_canvas_composite.html
[32]: http://tympanus.net/Tutorials/TexturedText/index8.html
[33]: http://www.rekim.com/tag/globalcompositeoperation/
[34]: http://www.html5rocks.com/en/tutorials/canvas/texteffects/
[35]: http://tympanus.net/Tutorials/TexturedText/index9.html
[36]: http://tympanus.net/codrops/2011/12/12/experiments-with-background-clip-text/
[37]: http://trentwalton.com/bgclip/
[38]: http://codepen.io/
[39]: http://codepen.io/lbebber/pen/xrwja
[40]: http://css-tricks.com/examples/ImageReplacement/
[41]: http://css-tricks.com/examples/ImageReplacement/
[42]: http://www.zeldman.com/2012/03/01/replacing-the-9999px-hack-new-image-replacement/
[43]: http://nicolasgallagher.com/another-css-image-replacement-technique/
[44]: http://scottkellum.com/2013/10/25/the-new-kellum-method.html

[textured text]: img/textured-text-demo1.png
[text with splatter effect]: img/textured-text-demo2.png
[an image with alpha opacity]: img/splatter-mask_1-codrops.png
[text with clipped background]: img/demo3.jpg
[text filled with a wooden pattern]: img/demo4.jpg
[gradient text]: img/demo5.jpg
[watercolor text]: img/demo6.jpg
[luminance image mask]: img/luminance-mask-codrops.png
[water textured text]: img/demo7.jpg
[text with fire texture]: img/demo8.jpg
[canvas globalCompositeOperation examples]: img/canvas-composite.png
[scratched text]: img/demo9.jpg
[brush strokes]: img/brushed-codrops.png