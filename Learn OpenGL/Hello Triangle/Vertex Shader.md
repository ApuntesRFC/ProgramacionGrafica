> [!summary]  
> A **vertex shader** runs once per vertex and is responsible for transforming each vertex’s position into **clip-space**.  
> In **modern OpenGL (3.3+)**, you must write and compile at least a **vertex shader** and a **fragment shader** to render anything.

---

### Step 1 — Vertex Shader Source (GLSL)

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```

---

### Explanation

|Element|Meaning|
|---|---|
|`#version 330 core`|GLSL version matching OpenGL 3.3 Core Profile.|
|`layout (location = 0)`|Binds this input variable to **vertex attribute index 0**.|
|`in vec3 aPos`|Input vertex attribute — 3D position for each vertex.|
|`gl_Position`|Built-in output variable; final clip-space position of the vertex.|
|`vec4(aPos, 1.0)`|Converts a 3D position into a 4D homogeneous coordinate.|

> [!note]  
> The **`w`** component (here set to `1.0`) enables proper **perspective division** later in the pipeline.  
> It’s not a spatial dimension but a scaling factor used in projection transformations.

---

### Step 2 — Understanding Vectors in GLSL

GLSL uses **vector types** to represent geometric quantities:

|Type|Components|Common Use|
|---|---|---|
|`vec2`|(x, y)|2D positions, texture coordinates|
|`vec3`|(x, y, z)|3D positions, directions|
|`vec4`|(x, y, z, w)|Homogeneous coordinates, color (RGBA)|

> [!info]  
> You can access components via `.x`, `.y`, `.z`, `.w`.  
> Example:
> 
> ```glsl
> vec3 position = vec3(1.0, 0.5, -0.2);
> float xValue = position.x;
> ```

---

### Step 3 — Shader Output

At the end of the vertex shader, OpenGL expects a single **output position** in `gl_Position`:

```glsl
gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
```

> [!important]  
> This is the vertex’s final position in **clip space**.  
> After this, OpenGL performs perspective division and viewport mapping to turn it into 2D screen coordinates.

---

### Step 4 — Conceptual Role in the Pipeline

> [!info]
> 
> - Each vertex runs through this shader individually.
>     
> - The shader transforms input vertex data → output clip-space position.
>     
> - Later stages (primitive assembly, rasterization, fragment shader) operate on the results.
>     

---

### Step 5 — Minimal Vertex Shader Behavior

This example is the **simplest possible** shader:

- Takes position directly as input.
    
- Performs **no transformations** (model, view, or projection).
    
- Outputs vertices already in NDC range (−1 to 1).
    

> [!tip]  
> In real applications, vertex data is usually **not in NDC**, so you’ll later apply **matrices** (Model, View, Projection) to convert from world space → clip space.

---

### Step 6 — Summary

> [!important]
> 
> - The vertex shader runs once per vertex.
>     
> - It must output a `gl_Position` (vec4).
>     
> - Input attributes are defined using `layout(location = n) in`.
>     
> - You must write and compile this shader before linking it into a program.
>     
> - Together with a **fragment shader**, it forms the minimal programmable pipeline needed to render your first triangle.
>