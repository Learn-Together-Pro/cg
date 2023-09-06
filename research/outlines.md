Outlines is a common technique used in games for highlighting objects or styling. There are several algorithms to draw outlines:

### Edge Detection
The idea is to detect discontinuities in the scene to render outlines there. It can be detected using several data sources: depth buffer, color buffer, normal vectors. Several methods can be combined together for better detection. Custom data buffers can be supplied to the algorithm to improve detection or select objects that will have an outline.

Advantages:
- Simple to make if you have buffers to analyze data from
- Works good for full-screen outlining

Disadvantages:
- It's hard to get it work for some local image part, special data should be supplied in some way.

### Vertex Extrusion
The idea is to draw the exact copy of the mesh behind the original mesh to form the outline. For second mesh to be the outline it should be made larger. For that we use **vertex extrusion**: we move the vertices in the direction of the normal.

This should be done in clip space to remove perspective projection artifacts (make outline width identical in every point).

When rendering, outline mesh should be rendered with ***front looking triangles culled*** or using ***stencil mask***.

One can use vertex normals for extrusion but this can lead to [artifacts](https://ameye.dev/notes/rendering-outlines/vertex-extrusion/normal-vector.png-900w.webp) for the objects with sharp corners (cubes,, for example). Custom normals can be supplied for that, but that requires additional work.

Advantages:
- Simple to make
- Has moderate performance

Disadvantages:
- Doesn't work for sharp edges if using standart normals
- Requires additional work when using custom extrusion normals

### Blurred Buffer
The idea is to render out object to the separate buffer (a.k.a **silhouette buffer**), blur that buffer and subtract the original buffer. Resulting buffer has the outline that can be put on the final image.

Advantages:
- No need to for extra mesh setup
- Easy soft/glowy outlines

Disadvantages:
- Additional render pass setup
- Can have bigger performance footprint, compared to other methods (depends on blur algorithm)
- Only outlines outside of the object silhouette can be drawn, if one desires to outline object parts regardless of whether they are inside/outside the silhouette of the object, previous methods should be used

### Jump Flood Algorithm
Jump flood algorithm has log(n) complexity (n is an image resolution) and is used in the construction of Voronoi diagrams and distance transforms. It also can be redesigned to be used for outline calculations.

We generate a texture where every point in object silhouette stores a 0 distance and other points store some "infinite" distance. We start the jump flood algorithm that is an algorithm for finding the closest distance to some object for every texture pixel. That means that after several steps each point will store the distance to the sillhouette and we can use this information to generate outlines.

Advantages:
- No need to for extra mesh setup
- Good for drawing fat outlines (without performance hit)

Disadvantages:
- Additional render pass setup
- Only outlines outside of the object silhouette can be drawn, if one desires to outline object parts regardless of whether they are inside/outside the silhouette of the object, previous methods should be used

### Links:
- [5 ways to draw an outline](https://ameye.dev/notes/rendering-outlines/)
- [Edge detection outline shader](https://roystan.net/articles/outline-shader/)
- [Vertex extrusion outline shader](https://www.videopoetics.com/tutorials/pixel-perfect-outline-shaders-unity/)
- [Rendering Soft outlines in Unreal Engine (Blurred buffer)](https://www.tomlooman.com/unreal-engine-soft-outline/)
- [The Quest for Very Wide Outlines (Jump Flood algorithm for outlines)](https://bgolus.medium.com/the-quest-for-very-wide-outlines-ba82ed442cd9)
- [Jump Flood algorithm (Wikipedia)](https://en.wikipedia.org/wiki/Jump_flooding_algorithm)
