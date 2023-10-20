# Vignette
[Vignette](https://en.wikipedia.org/wiki/Vignetting#Post-shoot) is a technique used to draw the attention to the center of the image by darkening or blurring the corners or edges (towards the [periphery](https://en.wiktionary.org/wiki/periphery)) of the frame. this effect is often used to create a more dramatic or cinematic look.

## How It Works?
to create a vignette effect our fragment shader runs for every pixel and darkens it based on it's distance from the center of the screen.<br>

## The Recipe
add a new ColorRect and make it Fullscreen and add a new ShaderMaterial to it and create a new shader for it([See How](./Chapters/Getting_Started/getting_started.html)).<br>
a new shader looks like this:
```glsl
shader_type canvas_item;

void fragment() {
	// Place fragment code here.
}

```
we add the following line above the ```fragment``` definition to declare a uniform that gives us access to the screen texture:
```glsl
uniform sampler2D SCREEN_TEXTURE : hint_screen_texture, filter_linear_mipmap;
```
### step 1: get the pixel distance from screen center
the ```SCREEN_UV``` is the current pixel's uv coordinates. uv coorditates are between 0-1 and therefore the center of the screen would be at (0.5, 0.5) coordinates.<br>
we store the distance of the current pixel from the center of the screen in a variable called```dist```.
```glsl
float dist = distance(SCREEN_UV, vec2(0.5, 0.5));
```
>note that the calculated distance is not in *pixels* but instead a normalized distance in uv coordinates. we could calculate the pixel position by multiplying the uv with ```SCREEN_PIXEL_SIZE```but we won't cause we are going to use the distance as the interpolation factor and we need it to be normalized anyway.<br>
>we can use ```vec2(0.5)``` to get the center of the screen as well, the constructor will create a vec2 with 0.5 for both X and Y.

### step 2: create vignette mask
let's add two more uniforms for the radius and sharpness of the vignette so that we can control those properties in the material inspector.
```glsl
uniform float sharpness;
uniform float radius;
```
then we can use them in a smoothstep function to calculate the vignette mask in this pixel.
```glsl
float vignette_mask = smoothstep(clamp(sharpness, 0, radius), radius, dist);
```

### step 3: darken the screen with the mask

we use the vignette mask to lerp the screen into black:
```glsl
vec4 screen = texture(SCREEN_TEXTURE, SCREEN_UV);
screen = mix(screen, vec4(0, 0, 0, 1), vignette_mask);
```
we sample the current pixel's color into a variable called ```screen``` and then we lerp it into the ```vec4(0, 0, 0, 1)``` with the vignette mask.<br>
if the distance of current pixel with the center of the screen is zero, then the calculated mask will also be zero for this pixel and when we use that as the interpolation factor to go from the pixel color into black, it will result in the pixel color.<br>
we can then assign the calculated color to this pixel:
```
COLOR = screen;
```
we can add another uniform for the color of the vignette effect:
```uniform vec4 vignette_color : source_color = vec4(0, 0, 0, 1);```
the ```source_color``` makes this vec4 show as a color in the material inspector.
![material inspector]()
and then use that to lerp to:
```
screen = mix(screen, vignette_color, vignette_mask);
```
![a gif to show how to control the vignette color from the inspector]()
here's the final shader:
```glsl
shader_type canvas_item;

uniform sampler2D SCREEN_TEXTURE : hint_screen_texture, filter_linear_mipmap;
uniform float sharpness;
uniform float radius;
uniform vec4 vignette_color : source_color = vec4(0, 0, 0, 1);

void fragment() {
	float dist = distance(SCREEN_UV, vec2(0.5));
	float vigentte_mask = smoothstep(clamp(sharpness, 0, radius), radius, dist);
	vec4 screen = texture(SCREEN_TEXTURE, SCREEN_UV);
	screen = mix(screen, vignette_color, vignette_mask);
	COLOR = screen;
}

```