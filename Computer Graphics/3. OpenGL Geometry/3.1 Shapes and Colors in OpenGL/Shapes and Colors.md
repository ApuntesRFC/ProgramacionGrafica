
> [!summary]
**Core OpenGL Concepts** — foundational principles used in modern graphics programming.  
Everything visible on screen is built from triangles, vertex data, and GPU-driven interpolation.

---

### Basic Primitives

> [!info]
> - All shapes in OpenGL are ultimately built from **points**, **lines**, and **triangles**.  
> - Curves and complex surfaces are **approximated by many small triangles**.  
> - Legacy primitives such as `GL_QUADS` and `GL_POLYGON` were **removed** — always use **triangles**.

> [!example]
> - `GL_POINTS` → individual dots  
> - `GL_LINES`, `GL_LINE_STRIP` → connected edges  
> - `GL_TRIANGLES`, `GL_TRIANGLE_STRIP`, `GL_TRIANGLE_FAN` → most common and efficient for rendering

Triangles are ideal because:
> - They are **always planar**.  
> - They can approximate any surface.  
> - GPU hardware is optimized to process them.

---

### Vertices and Attributes

Each **vertex** defines one point in space and carries **attributes** such as:
> - Position `(x, y, z)`  
> - Color `(r, g, b)`  
> - Normal vector `(nx, ny, nz)`  
> - Texture coordinates `(u, v)`

> [!note]
> The GPU **interpolates** vertex attributes across the surface of triangles, generating **fragments** (potential pixels) with smoothly varying color, lighting, or texture.

In modern OpenGL:
> - Vertex data is stored in **buffers**:  
>   - `VBO` → Vertex Buffer Object (stores raw vertex data)  
>   - `VAO` → Vertex Array Object (describes layout and state)  
> - The old `glBegin()` / `glEnd()` system is deprecated.

> [!example]
> ```c
> glBindVertexArray(VAO);
> glDrawArrays(GL_TRIANGLES, 0, 36);
> ```

---

### Color and Transparency

Colors in OpenGL use the **RGBA** format:
$$
(R, G, B, A)
$$
where $A$ = **alpha**, controlling opacity (1 = fully opaque, 0 = fully transparent).

> [!important]
> To use transparency, blending must be **enabled and configured**:

```c
glEnable(GL_BLEND);
glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
````

This formula computes the final color per pixel as:  
$$  
C_{\text{final}} = A_{\text{src}} \cdot C_{\text{src}} + (1 - A_{\text{src}}) \cdot C_{\text{dst}}  
$$

> [!warning]  
> Transparency in 3D is **not handled automatically** by depth testing.  
> You must **render transparent objects last**, after all opaque ones.

---

### Depth Test (Z-buffer)

Each pixel stores a **depth value (z)** — its distance from the camera.  
When rendering:

> - A fragment replaces the previous one **only if it’s closer** (smaller z).
>     
> - This ensures correct visibility between overlapping objects.
>     

Enable and clear depth testing:

```c
glEnable(GL_DEPTH_TEST);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

> [!note]
> 
> - Depth values range from **0 (near)** to **1 (far)**.
>     
> - The Z-buffer automatically prevents background objects from overwriting closer ones.
>     

> [!warning]  
> Using an excessively large z-range (e.g., 0.001–10,000) **reduces precision**, leading to **Z-fighting** — small flickering errors between surfaces.

> [!tip]  
> Always:
> 
> - Use a reasonable **near/far plane** range.
>     
> - Render **opaque objects first**, **transparent objects last**.
>     

---

### Key Takeaways

> [!note]
> 
> - Every object → built from **triangles**.
>     
> - Each triangle → defined by **vertices** with attributes.
>     
> - GPU interpolates vertex data to produce smooth results.
>     
> - Depth testing ensures visibility; blending handles transparency.
>     
> - Render order matters when using alpha blending.
>     

---

**Learn More:**  
📘 [LearnOpenGL — Getting Started with Primitives](https://learnopengl.com/)