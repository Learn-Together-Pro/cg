# Key concepts of WebGL

Key concepts

## Function having impact on VAO

- `vertexAttribPointer()` / `vertexAttribDivisor()`
- `enableVertexAttribArray()` / `disableVertexAttribArray()`
- `bindBuffer( ELEMENTS_ARRAY_BUFFER )`

## Transparency and Depth handling

1. Enable depth testing if depth is taken into account
2. Enable blending & set a blending function if transparent objects exists
3. Draw opaque things first, in any order, then draw transparent objects.
4. Draw transparent things back to front if transparent objects overlap, but not in depth.
5. Disable writing to the depth buffer or change the depth function if transparent objects overlap in depth ( coplanar ).
