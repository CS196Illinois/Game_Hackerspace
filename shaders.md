# Intro to Shaders
Take off your shades, because it looks like this class  
⊂(▀¯▀⊂)  
is about to get a lot more graphic  
( •_•)>⌐■-■  
YEEAAAAAAAAAAAAAH

## What is a Shader?
A shader, sometimes called a graphics shader, is a type of program or function that runs after the rest of the rendering code is done, and directly affects the pixels. (Or sometimes vertices, if you are doing 3D code). This means that instead of working with sprites and images, you're working directly with pixels, telling the computer how it should modify the pixels that its being given. 

For example, if you wanted to have a sequence in your game that was a flashback, you could enable a shader that took every pixel and converted them from colorful to grayscale.

## Creating Shaders
You may be wondering how you can make a shader. Do you need to use a game engine, or programming language? The answer is: sort of. Because shaders are such a widely used concept that are so fundamental to graphics programming, most shaders are programmed in a langauge called GLSL. It is similar to C, if you have used that. However, once you learn how to write shaders, that knowledge is applicable to almost any game engine or framework that you use.  
In our case, we will be writing our shaders on a website called [Shadertoy](https://www.shadertoy.com) - thank glob, no one needs to install anything.

### Shadertoy
[Shadertoy](https://www.shadertoy.com) is a website that lets you write shaders, and also lets you play around with them and share them in a bunch of fun ways. You can set it up so that youtube videos, images, or other code are fed into your shader, so it's really great to experiment with.  
Go to the website and click the new button.

### Starting a new Shader
When you open up a new shader on Shadertoy, you should see some code on the rightm and the actual output of the shader on the left, along with some controls.

#### What is going on here?
The default code for a new shader is


```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
	vec2 uv = fragCoord.xy / iResolution.xy;
	fragColor = vec4(uv,0.5+0.5*sin(iGlobalTime),1.0);
}
```

Here is what this function does: it returns a single pixel that has a **r**, **g**, **b**, and **a** values. R is the amount of red, g is the amount of green, b is the amount of blue, and a is the opacity. Since it is a **pixel** shader, it this function is run on every single pixel on the screen and returns a single pixel each time.  
To help accomplish this, we given the coordinates of the input pixel so that we can do something interesting rather than just output a solid color. In the code above, `out vec4 fragColor` is our output pixel and `in vec2 fragCoord` are the pixel coordinates. `vec4` means it is a vector with 4 elements **(r,g,b,a)**, and `vec2` means it is a vector with 2 elements **(x,y)**.  
The r, g, and b, values can range from 0 - 1.0, 0 meaning none at all and 1.0 meaning fully colored. So, `(1, 0, 0)` would be red, `(0, 0, 0)` would be black, and `(.5, .5, .5)` would be gray. The last value, the **a** or **alpha channel**, also ranges from 0 to 1.0, but 0 means fully transparent and 1.0 means fully opaque. So, if we wanted to have a see-through purple color, we would create a `vec4` by writing `vec4(1, 0, 1, 0.5)`  

Instead of returning a value like you would usually do in python, we just assign what we want to return to `fragColor`, hence `fragColor = vec4(uv,0.5+0.5*sin(iGlobalTime),1.0);`.

#### Making a solid color shader
By now, if you understand the underlying idea behind tne shader, you should see how simple it can be to make a shader that just produces a solid shader. All we have to do is write something alnog the lines of 

```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
	vec4 solidGreen = vec4(0.0, 1.0, 0.0, 1.0);
	fragColor = solidGreen;
}
```
and voila, a solid green shader!

#### Utilizing the pixel coordinates
Making a shader that just sets everything to be a solid color is pretty boring, given the potential power at our hands. How can we make things more interesting? To start off with, we can take advantage of hte pixel coordinates. We do this by using that `in vec2 fragCoord` argument. We have to write `fragCoord.xy` to get the coordinates of the pixel, and access each one individually by using `.x` or `.y`. This is demonstrated in the following codeblock, that sets the leftmost pixels to red and the rest to black.

```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 xy = fragCoord.xy; //We obtain our coordinates for the current pixel
    vec4 solidRed   = vec4(1, 0.0, 0.0, 1.0);
    vec4 solidBlack = vec4(0.0, 0.0, 0.0, 1.0);
    if(xy.x > 300.0){ //Arbitrary number, we don't know how big our screen is!
        fragColor = solidBlack;
    } else {
        fragColor = solidRed;
    }
}
```
This is nice, but it would be even better if we could make it so that we evenly split it. How do we do this when our resolution isn't fixed, you ask? 

#### Shader inputs
If you click the little tab that says shader inputs above the shadertoy code box, you get a list of inputs you can use to help write your shader. You have time, mouse coordinates, and other stuff, but we are concerned with the screensize - in this case, called `iResolution`. There are many ways to use this, but one way is to divide our `xy` vector by it, so we can treat the screen as a range of values from 0 to 1.0 in the same way we do colors and the alpha channel.
```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 xy = fragCoord.xy; //We obtain our coordinates for the current pixel
    xy.x = xy.x / iResolution.x; //We divide the coordinates by the screen size
    xy.y = xy.y / iResolution.y;
    // Now, if xy.x == 1.0, we are at the rightmost pixel, and if xy.x == 0, we are at the leftmost
    // Same thing for y
    
    vec4 solidRed   = vec4(1, 0.0, 0.0, 1.0);
    vec4 solidBlack = vec4(0.0, 0.0, 0.0, 1.0);
    if(xy.x > .5){// If we're further than halfway across the screen, set the pixel to black!
        fragColor = solidBlack;
    } else {
        fragColor = solidRed;
    }
}
```
