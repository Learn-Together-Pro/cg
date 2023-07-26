## Colorspaces

Godot 4 has [2 rendering engines](https://docs.godotengine.org/en/stable/contributing/development/core_and_modules/internal_rendering_architecture.html). First one is based on Vulkan, second one is based on OpenGL ES 3.0. First one has 2 versions: `Forward+` that targets  high-end devices and `Mobile` that targets low-end and mobile devices. Second render is called `Compatibility`, it targets web and old platforms that do not support new APIs like Vulkan.

The question arises of the color spaces in which custom shaders work in different renderers.

### Vulkan based 3D render
New Vulkan based 3D render expects user to do all calculations in linear color space and give the result also in linear color space. sRGB conversion is done by Godot automatically in subsequent rendering stages.

Godot has special `source_color` hint that is placed on uniforms to mark them as storing color:
```glsl
uniform vec3 my_color_uniform: source_color;
uniform sampler2D my_color_texture: source_color;
```
When using `Forward+ 3D` or `Mobile 3D` render, Godot will do **automatic sRGB -> linear conversion** for all uniforms marked with this attribute.
User can disable automatic conversion by removing this hint.
(Note, that Godot uses this hint to enable editor color picker for color uniforms, so attribute removal can harm ease of use a bit)

### Other renders
Other renders (Vulkan 2D and OpenGL ES 3.0 3D/2D) expect the shader output to be in sRGB color space. Also, Godot doesn't do any automatic conversions for uniforms, marked with `source_color` attribute. If user wants to do blending (or other color transformations), one should transform input colors to linear color space manually and then transform the result to sRGB.

### OUTPUT_IS_SRGB flag
Godot spatial shaders (shaders for rendering 3D objects) have special variable `OUTPUT_IS_SRGB` that is set to `true` for OpenGL based 3D render and to `false` for Vulkan based 3D shader. It is `true` if shader result is expected to be is sRGB color space.

### Summary

All of the above can be summarized in a table:

|                      | Expects uniform input to be  | Converts uniform automatically  | Expects output to be |
|----------------------|------------------------------|---------------------------------|----------------------|
| Forward+ / Mobile 3D | sRGB                         | Yes (sRGB -> Linear)            | Linear               |
| Forward+ / Mobile 2D | No expectations              | No                              | sRGB                 |
| Compatibility 3D     | No expectations              | No                              | sRGB                 |
| Compatibility 2D     | No expectations              | No                              | sRGB                 |
```
