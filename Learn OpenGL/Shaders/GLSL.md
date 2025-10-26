> [!summary]  
> **Shaders** are small, isolated programs that run on the **GPU** and process data for specific stages of the **graphics pipeline**.  
> They are written in **GLSL**, a C-like language specialized for graphics, vectors, and matrices.

---

### What shaders do

> [!info]
> 
> - A shader transforms **inputs → outputs** (e.g., vertex positions → final colors).
>     
> - Each shader runs independently per vertex or per fragment.
>     
> - Shaders **cannot directly communicate**; they exchange data only via **inputs and outputs** between stages.
>     

---

### Basic shader structure (GLSL)

```glsl
#version version_number

in type  in_variable_name;    // input from previous stage (or vertex attributes)
out type out_variable_name;   // output to next stage
uniform type uniform_name;    // global, read-only variable (set from CPU)

void main()
{
    // process input(s)
    // ...
    out_variable_name = ...;  // send processed result to next stage
}
```

> [!note]  
> Every shader starts with:
> 
> - A `#version` directive (e.g., `#version 330 core`)
>     
> - Input and output variable declarations
>     
> - A `main()` function (entry point)
>     

---

### Shader types (programmable stages)

|Shader Type|Runs for|Typical Role|
|---|---|---|
|**Vertex Shader**|Each vertex|Transforms vertex positions into clip space|
|**Fragment Shader**|Each pixel fragment|Computes final pixel color|
|**Geometry Shader**|Each primitive|Optionally creates or alters geometry|
|_(Later stages include tessellation shaders and compute shaders.)_|||

> [!tip]  
> In modern OpenGL, at least **vertex** and **fragment** shaders are mandatory.

---

### Vertex attributes

When referring to a **vertex shader**, each `in` variable represents a **vertex attribute** (e.g., position, color, normal).

Example:

```glsl
layout (location = 0) in vec3 aPos;   // vertex position
layout (location = 1) in vec3 aColor; // vertex color
```

These correspond to attribute slots defined in your OpenGL code with:

```cpp
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, stride, (void*)0);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, stride, (void*)(3 * sizeof(float)));
```

---

### Uniforms (global inputs)

> [!info]  
> **Uniforms** are constant values shared by all shader invocations (e.g., transformation matrices, light positions).  
> You set them once per frame or per draw call from your application code.  
> Example:
> 
> ```glsl
> uniform mat4 model;
> uniform mat4 view;
> uniform mat4 projection;
> ```

---

### Querying hardware attribute limits

You can check how many vertex attributes your GPU supports:

```cpp
int nrAttributes;
glGetIntegerv(GL_MAX_VERTEX_ATTRIBS, &nrAttributes);
std::cout << "Maximum vertex attributes supported: " << nrAttributes << std::endl;
```

> [!note]  
> OpenGL guarantees **at least 16** vertex attributes (each a 4-component vector).  
> Modern GPUs typically support far more.

---

### Key takeaways

> [!important]
> 
> - **Shaders = GPU programs** for each pipeline stage.
>     
> - Written in **GLSL** with a strict structure.
>     
> - They only interact through **inputs**, **outputs**, and **uniforms**.
>     
> - The vertex shader’s inputs correspond to **vertex attributes** defined in your OpenGL code.
>     
> - Always start shaders with `#version` matching your OpenGL version (e.g., `#version 330 core`).
>