# Intro

Ready to dive into the exciting world of [Post-Processing](https://en.wikipedia.org/wiki/Video_post-processing#Uses_in_3D_rendering) in video-games? You'll be amazed at the variety of things that are done with post-processing, from physical/environmental effects like fog, ambient occlusion, and reflections, to camera-related effects such as bloom, depth of field, and motion blur. You can get a filmic look in your game with tonemapping, film grain, and vignette, you can enhance the final image with anti-aliasing and upscaling. 
and let's not forget about special effects like night-vision, infrared-camera, and underwater effect.<br>
you can do just so much with post-processing! <br>
let's begin! <br>

## Getting Started with Post-Processing in Godot

we can create a post-processing effect using either a ColorRect [(Read The Docs)](https://docs.godotengine.org/en/stable/tutorials/shaders/custom_postprocessing.html) or using a quad mesh that uses a vertex shader to cover the screen at all times [(Read The Docs)](https://docs.godotengine.org/en/stable/tutorials/shaders/advanced_postprocessing.html). <br>
the latter gives you access to the depth texture and you can use it to create effects that use depth, but most 'Recipes' of this book doesn't need to read the depth buffer and so they will be made with a canvas shader on a ColorRect node.<br>

create color rect
make it fullscreen
add shader
read the screen

