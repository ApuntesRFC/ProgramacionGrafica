> [!summary]
Overview of CPU–GPU collaboration in computer graphics, OpenGL architecture, programmable pipeline, and shader programming with GLSL.

---

### CPU in Graphics

The **Central Processing Unit (CPU)** manages general-purpose computations and coordinates the graphics pipeline. It prepares geometry, textures, and rendering commands before sending them to the GPU.

> [!example]
> - Example: loading a 3D model from disk, calculating its position, and sending vertex data to the GPU.

> [!note]
> - Limitation: CPUs are optimized for **sequential tasks**, not for the massive **parallelism** required in real-time graphics.

---

### GPU in Graphics

The **Graphics Processing Unit (GPU)** is specialized hardware designed for parallel processing, capable of handling thousands of operations simultaneously.

> [!info]
> - Strength: executes **many vertex and pixel operations** concurrently.  
> - Role: handles **shaders**, **rasterization**, and **transformations** efficiently.

---

### Graphics API

A **Graphics API** provides an abstraction layer between the application and the GPU, allowing developers to render graphics without managing hardware directly.

> [!example]
> - Common APIs: **OpenGL**, **DirectX**, **Vulkan**, **Metal**.  

> [!important]
> - Benefit: enables **cross-platform development** and **hardware-independent rendering**.

---

### Client/Server System

In OpenGL’s architecture, the system follows a **client/server model**:

> [!info]
> - **Client:** the application that sends geometry, textures, and rendering commands.  
> - **Server:** the GPU and its driver, which process those commands to produce the image.

This design separates **resource management** (client side) from **execution** (server side).

---

### How Does OpenGL Work?

**OpenGL** operates as a **state machine** and a **specification-based API**.  
It defines how data flows through the rendering pipeline rather than how it is implemented.

> [!example]
> 1. The application sends geometry, textures, and shaders.  
> 2. OpenGL stores these in GPU memory.  
> 3. The pipeline processes data through:
>    - Vertex processing  
>    - Primitive assembly  
>    - Rasterization  
>    - Fragment processing  
> 4. The final image is written to the **frame buffer**.

> [!note]
> - OpenGL is a **specification**, not a standalone program; each hardware vendor provides its own implementation.

---

### Vertex Buffer Objects (VBOs)

**VBOs** are GPU memory buffers that store vertex data such as positions, normals, and texture coordinates.

> [!example]
> - Advantage: instead of sending vertices one by one, they are uploaded once to GPU memory for fast access.  
> - Commonly used alongside **Vertex Array Objects (VAOs)** to define how vertex data is read.

---

### Texture Objects

**Texture objects** are GPU-resident data structures used to store image data that can be mapped onto surfaces.

> [!example]
> - Benefit: reuse and efficient access to texture data.  
> - Types include **1D**, **2D**, **3D** textures, and **cube maps** for environment reflections.

---

### Programmable Machine (OpenGL)

Modern OpenGL treats the GPU as a **programmable machine**.  
Developers can write small programs (shaders) to customize how vertices and fragments are processed.

> [!note]
> - Contrast: older OpenGL used a **fixed-function pipeline** with predefined operations only.

---

### Shaders

**Shaders** are small GPU programs that control rendering pipeline stages. They are written in **GLSL (OpenGL Shading Language)**.

> [!example]
> - Shaders can compute lighting, apply textures, or simulate effects such as **water**, **fire**, or **reflections**.

> [!tip]
> - Shaders replace the fixed-function pipeline, giving developers full control over the rendering process.

---

### Vertex Shader

The **vertex shader** runs once per vertex and is responsible for transforming vertex data.

> [!info]
> - Input: vertex position, color, normal, texture coordinates.  
> - Output: transformed position in clip space, optionally passing attributes to the next stage.

> [!example]
> - Common task: applying the **model–view–projection matrix** to position objects correctly in the scene.

---

### Fragment Shader

The **fragment shader** runs once per fragment (potential pixel).  
It determines the final color of each pixel before it is written to the frame buffer.

> [!info]
> - Input: interpolated data from the vertex shader (e.g., color, texture coordinates).  
> - Output: final pixel color.

> [!example]
> - Example: apply a texture, compute lighting per pixel, or add transparency.

---

### GLSL (OpenGL Shading Language)

**GLSL** is a C-like programming language for writing shaders in OpenGL.  
It provides built-in types for vectors, matrices, and mathematical functions used in graphics.

> [!info]
> - Features: vector operations (`vec2`, `vec3`, `vec4`), matrix math, and trigonometric functions.

> [!example]
> - Simple fragment shader that outputs a solid red color:

```c
void main() {
    gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0); // sets pixel color to red
}
```
