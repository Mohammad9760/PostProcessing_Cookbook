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
### step 1: get the pixel distance from screen center
```glsl
float dist = distance(SCREEN_UV, vec2(0.5, 0.5));
```

### step 2: create vignette mask
```glsl
float vigenette_mask = smoothstep(clamp(sharpness, 0, radius), radius, dist);
```

### step 3: darken the screen with the mask
```glsl
vec4 screen = texture(SCREEN_TEXTURE, SCREEN_UV);
screen = mix(screen, vignette_color, vigenette_mask);
COLOR = screen;
```


here's the final shader:
```glsl
shader_type canvas_item;

uniform sampler2D SCREEN_TEXTURE : hint_screen_texture, filter_linear_mipmap;
uniform float sharpness;
uniform float radius;
uniform vec4 vignette_color : source_color = vec4(0, 0, 0, 1);

void fragment() {
	float dist = distance(SCREEN_UV, vec2(0.5));
	float vigenette_mask = smoothstep(clamp(sharpness, 0, radius), radius, dist);
	vec4 screen = texture(SCREEN_TEXTURE, SCREEN_UV);
	screen = mix(screen, vignette_color, vigenette_mask);
	COLOR = screen;
}

```