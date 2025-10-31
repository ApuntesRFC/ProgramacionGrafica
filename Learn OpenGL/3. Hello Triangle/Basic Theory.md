> [!summary]  
> **OpenGL’s graphics pipeline** transforms 3D vertices into 2D colored pixels.  
> It processes geometry step-by-step on the **GPU**, where each stage specializes in one transformation.  
> Developers control certain stages with small GPU programs called **shaders** written in **GLSL**.

---

### Overview: From 3D to 2D

OpenGL renders 3D scenes to a **2D window**.  
The **graphics pipeline** manages this conversion through multiple stages:

> [!info]
> 
> - **Stage 1:** Converts 3D vertex data into transformed coordinates.
>     
> - **Stage 2:** Converts transformed coordinates into **screen pixels (fragments)** with color.
>     

This system runs on thousands of GPU cores, each processing vertices or fragments in parallel.

---

### Programmable Stages and Shaders

| Stage                          | Description                                                                                                   | Programmable? |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------- |
| **Vertex Shader**              | Processes each vertex (positions, colors, normals). Transforms from model to screen coordinates.              | ✅ Required    |
| **Primitive Assembly**         | Collects vertices into primitives (points, lines, triangles).                                                 | ❌             |
| **Geometry Shader**            | Can generate or modify primitives on the fly.                                                                 | ✅ Optional    |
| **Rasterization**              | Converts primitives into 2D fragments (potential pixels). Performs clipping (discards out-of-view fragments). | ❌             |
| **Fragment Shader**            | Computes the color of each fragment (light, texture, shadow, etc.).                                           | ✅ Required    |
| **Alpha/Depth/Blending Stage** | Combines fragments, applies transparency, and resolves visibility (depth test).                               | ❌             |

> [!note]  
> You must write at least a **vertex shader** and **fragment shader** in modern OpenGL (Core Profile).  
> There are **no default shaders** on the GPU.

---

### Vertex Data and Primitives

The input to the pipeline is a set of **vertices**, where each vertex can contain:

- 3D position (`vec3`)
    
- Color (`vec3` or `vec4`)
    
- Optional data (normals, texture coords, etc.)
    

> [!example]  
> To render a triangle, we pass three 3D vertices to the pipeline as an array of vertex attributes.

```cpp
float vertices[] = {
    // positions       // colors
     0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,
    -0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,
     0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f
};
```

When drawing, we specify the **primitive type**:

```cpp
glDrawArrays(GL_TRIANGLES, 0, 3);
```

|Primitive Type|Description|
|---|---|
|`GL_POINTS`|Each vertex is a point.|
|`GL_LINES`, `GL_LINE_STRIP`|Connect vertices with lines.|
|`GL_TRIANGLES`|Groups of three vertices form triangles.|

---

### Graphics Pipeline Stages (Simplified)

```text
Vertex Data
   ↓
Vertex Shader       ← (transform, per-vertex processing)
   ↓
Primitive Assembly  ← (forms triangles, lines, etc.)
   ↓
Geometry Shader     ← (optional: can emit new vertices)
   ↓
Rasterization       ← (convert triangles → fragments)
   ↓
Fragment Shader     ← (compute pixel color)
   ↓
Depth/Alpha/Blending← (combine fragments, handle transparency)
   ↓
Framebuffer Output  ← (final pixels on screen)
```

---

### Fragment and Final Output

A **fragment** contains all data needed to produce one pixel:

- Interpolated position, depth, color, texture coordinates.
    

The **fragment shader** runs once per fragment, determining its final color.  
After that, OpenGL may perform:

- **Depth testing** (hide covered fragments).
    
- **Alpha blending** (transparency).
    
- **Stencil operations** (masking).
    

> [!tip]  
> The color you output in the fragment shader might still change due to blending or depth testing in this final stage.

---

### Key Takeaways

> [!important]
> 
> - OpenGL’s pipeline converts 3D vertex data → 2D pixels through specialized stages.
>     
> - You write **shaders** to customize how vertices and fragments are processed.
>     
> - At minimum, define:
>     
>     - **Vertex Shader:** transforms vertex positions.
>         
>     - **Fragment Shader:** computes final pixel color.
>         
> - **Primitives** tell OpenGL how to interpret vertex groups (triangles, lines, points).
>     
> - The **geometry shader** and other advanced stages are optional and usually left to defaults.
>     

---

### Next Step

> [!next]  
> In the following chapter, you’ll write your first **GLSL shaders** (vertex + fragment),  
> compile them, and render your first triangle using this full pipeline.