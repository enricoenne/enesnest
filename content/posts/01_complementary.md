---
title: "in search of complementary colors"
date: 2023-05-28T22:04:25+02:00
tags:
  - programming
  - Processing
  - colors
---

## the "problem"

One day I was playing around with some colors and their complementaries searching for some nice-looking pairs. I was working with a black background and when I stumbled upon the pair yellow-blue, I realized how dark the blue was compared to its partner, which made my blue channel very hard to visualize.

I wanted to find the pairs of complementary colors with the same perceived brightness. First of all I needed a way to quantify the perceived brightness, since the brightness of the HSB (Hue Saturation Brightness) is calculated as the maximum between the three RGB components in percentage, it wasn't useful, because a green (0 - 255 - 0) and a blue (0 - 0 - 255) would have the same B for clearly different perceived brightnesses.

## some cool formulas

I found this [very useful thread](https://stackoverflow.com/questions/596216/formula-to-determine-perceived-brightness-of-rgb-color), which contains multiple different solutions to the problem. I took the easiest one ignoring the possible corrections (this might affect the accuracy of the final result, one day maybe I will come back to write a better version).

Working in the 3d space RGB where
	
	0 <= r <= 1
	0 <= g <= 1
	0 <= b <= 1

I used this linear formula to calculate the perceived brightness I was interested in

	L(r,g,b) = 0.2126 r + 0.7152 g + 0.0722 b
	
Now, if I choose a value of L between 0 and 1 I will get a plane of colors with the same L.

To obtain the complementary I used the formula
	
	C(r,g,b) = (1-r, 1-g, 1-b)
	
which cause a central simmetry around the point (0.5, 0.5, 0.5).

If I am looking for a color whose complementary has the same L I will get

	0.2126 r + 0.7152 g + 0.0722 b = 0.2126 (1-r) + 0.7152 (1-g) + 0.0722 (1-b)
	2 (0.2126 r + 0.7152 g + 0.0722 b) = 0.2126 + 0.7152 + 0.0722
	0.2126 r + 0.7152 g + 0.0722 b = 0.5
	
which identify the planes of all the pairs of colors I am interested on. By the way, this colors are also all the colors with L equals to 0.5. Now, looking at the formula, it makes sense the a color such as (0.5, 0.5, 0.5) belongs to the pair, being the complementary of itself and well, having the same L as itself. How are the other colors though?

I used [wolfram alpha](https://www.wolframalpha.com/input?i=0.2126+x+%2B+0.7152+y+%2B+0.0722+z+%3D+0.5%2C+0%3Cx%3C1%2C+0%3Cy%3C1%2C+0%3Cz%3C1+) to get formulas to calculate the components.

## some little code

To visualize all the colors I moved to [Processing](https://processing.org/), which I used to write this short code

```java
void setup() {
  size(500, 500);

  loadPixels();
  
  for (int x = 0; x < width; x++) {
    float min_g = (545700 - (float) 1063*x/width * 255)/3576;
    float max_g = (637500 - (float) 1063*x/width * 255)/3576;
    
    //00
    float min_b = (637500- (float) 1063*x/width*255 - 3576*max_g)/361;
    //254,29
    float max_b = (637500- (float) 1063*x/width*255 - 3576*min_g)/361;
    
    for (int y = 0; y < height; y++) {
      int loc = x + y * width;
      
      int r = int((float) x/width * 255);
      int g = int(min_g + (float) y/height * (max_g - min_g));
      int b = int(max_b - (float) y/height * (max_b - min_b));
      
      pixels[loc] = color(r, g, b);
    }
  }
  
  updatePixels();
}
```
## colors!

Along the x axis the red component goes from 0 to 255, the green and the blue components changes along the y axis. The blue should go from 0 to 255, the green range instead is limited and dependent on the others.

{{< figure src="https://enesnest.neocities.org/complementary_plane.png" alt="image" caption="plane of same-perceived brightness complementary colors" class="big" >}}

Tadah!

First of all, the colors are pretty, which is the most important thing. Probably I will end up using only the colors on the extreme right and left, because the central one are kind of greyish with low contrast. I was thinking about the top ones, but then the complementaries are the bottom ones, and I really dislike that weird brown.
