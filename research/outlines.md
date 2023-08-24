Outlines is a common technique used in games for highlighting objects or styling. There are several algorithms to draw outlines:

### Edge Detection
The idea is to detect discontinuities in the scene to render outlines there. It can be detected using several data sources: depth buffer, color buffer, normal vectors. Several methods can be combined together for better detection. Custom data buffers can be supplied to the algorithm to improve detection or select objects that will have an outline.

Advantages:
- Simple to make if you have buffers to analyze data from
- Works good for full-screen outlining
Disadvantage:
- It's hard to get it work for some local image part, special data should be supplied in some way.

### Vertex Extrusion
The idea is to draw the exact copy of the mesh behind the original mesh to form the outline. For second mesh to be the outline it should be made larger. For that we use **vertex extrusion**: we move the vertices in the direction of the normal.

This should be done in clip space to remove perspective projection artifacts (make outline width identical in every point).

When rendering, outline mesh should be rendered with ***front looking triangles culled*** or using ***stencil mask***.

One can use vertex normals for extrusion but this can lead to [artifacts](https://ameye.dev/notes/rendering-outlines/vertex-extrusion/normal-vector.png-900w.webp) for the objects with sharp corners (cubes,, for example). Custom normals can be supplied for that, but that requires additional work.

Advantages:
- Simple to make
- Has moderate performance
Disadvantage:
- Doesn't work for sharp edges if using standart normals
- Requires additional work when using custom extrusion normals

### Blurred Buffer
The idea is to render out object to the separate buffer (a.k.a **silhouette buffer**), blur that buffer and subtract the original buffer. Resulting buffer has the outline that can be put on the final image.

Advantages:
- No need to for extra mesh setup
- Easy soft/glowy outlines
Disadvantage:
- Additional render pass setup
- Bigger performance hit, compared to other methods

### Jump Flood Algorithm
TODO


### Links:
- [5 ways to draw an outline](https://ameye.dev/notes/rendering-outlines/)
- [The Quest for Very Wide Outlines](https://bgolus.medium.com/the-quest-for-very-wide-outlines-ba82ed442cd10)
