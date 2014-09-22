---
layout:     post
title:      Press-fit Kit
image:      img/pressfit_construction.jpg
number:     '01'
---

I go hiking a lot, and there are two things that annoy me to no end when I pull out my
iPhone to take pictures:

1. Panoramic videos of landscapes are usually shaky because it's hard to stabilize the phone.

2. Taking group pictures is no fun because the photographer is left out :(

I built a press-fit cardboard kit which solves both of these problems with only
8 oz of cardboard!

{% include image.html url="/img/pressfit_selfie_horizontal.jpg" description="Selfie-stick in 'Selfie' mode" %}

The selfie-stick has two "modes". The "Selfie" mode as pictured above, and a "SteadyCam"
mode which lets you take smooth videos by weighing down the lower half of the stick:

{% include image.html url="/img/pressfit_upright_stick.jpg" description="Selfie-stick in 'SteadyCam' mode" %}

{% include image.html url="/img/pressfit_stabilization_weight.jpg" description="The bottom of the selfie-stick is weighed down with a water bottle in order to stabilize the iPhone camera." %}

Here is a comparison between iPhone video filming with and without the selfie-stick:

<iframe style="display:block; margin: 0 auto; padding-bottom: 30px" width="630" height="473" src="//www.youtube.com/embed/tasFJG8ray8" frameborder="0" allowfullscreen></iframe>

The biggest learning curve for this project was the software, as I struggled to find
good 2d shape drawing software. I am very familiar with Adobe Illustrator, but it doesn't
contain good support for parametric shapes or boolean operations. I ended up settling on Rhino,
and I used Grasshopper to model the selfie-stick parameters. I definitely missed some features
from Illustrator, but I think that it helped to gain more familiarity with the Rhino package.

And finally, a selfie!

{% include image.html url="/img/pressfit_everyone_selfie.jpg" description="" %}





