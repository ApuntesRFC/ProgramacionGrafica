> [!summary]  
> To draw anything, you must first **send vertex data to the GPU**.  
> You define vertices in **Normalized Device Coordinates (NDC)** — where $x$, $y$, and $z$ each range from **−1.0 to 1.0** — and upload them to a **Vertex Buffer Object (VBO)** for fast access by the GPU.

---

### Step 1 — Define Vertex Data

A triangle needs **3 vertices**, each with 3D coordinates:

```cpp
float vertices[] = {
    -0.5f, -0.5f, 0.0f,  // left
     0.5f, -0.5f, 0.0f,  // right
     0.0f,  0.5f, 0.0f   // top
};
```

> [!info]
> 
> - OpenGL works in **3D**, but here we keep `z = 0.0` to make the triangle appear **2D**.
>     
> - All coordinates outside the range `[-1.0, 1.0]` are **clipped** and won’t be visible.
>     

---

### Step 2 — Normalized Device Coordinates (NDC)

After vertex processing, coordinates must be inside:

$$  
x, y, z \in [-1.0, 1.0]  
$$

|Axis|Meaning|Direction|
|---|---|---|
|**x**|Horizontal|−1 left → +1 right|
|**y**|Vertical|−1 bottom → +1 top|
|**z**|Depth|−1 near → +1 far|

> [!note]
> 
> - The origin $(0, 0)$ is **centered** on the screen (not top-left).
>     
> - After NDC, OpenGL maps them to **screen space** using the values from `glViewport`.
>     
> - These are then converted to **fragments** for the fragment shader.
>     

---

### Step 3 — Store Data on the GPU (VBO)

Sending data directly from CPU → GPU every frame is slow.  
Instead, we store vertices in GPU memory using a **Vertex Buffer Object (VBO)**.

```cpp
unsigned int VBO;
glGenBuffers(1, &VBO);                         // Generate 1 buffer ID
glBindBuffer(GL_ARRAY_BUFFER, VBO);            // Bind it as a vertex buffer
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

---

### Step 4 — Explanation of Each Call

|Function|Purpose|
|---|---|
|`glGenBuffers(n, &id)`|Creates `n` buffer object IDs.|
|`glBindBuffer(target, id)`|Binds the buffer so future calls affect it.|
|`glBufferData(target, size, data, usage)`|Copies vertex data from CPU → GPU memory.|

> [!important]  
> When a buffer is bound to `GL_ARRAY_BUFFER`, **all** subsequent calls affecting that target apply to the currently bound buffer.

---

### Step 5 — Understanding the `usage` Parameter

The last argument of `glBufferData` gives OpenGL a **hint** on how the data will be used:

|Usage Type|Meaning|
|---|---|
|`GL_STREAM_DRAW`|Data set once, used a few times.|
|`GL_STATIC_DRAW`|Data set once, used many times.|
|`GL_DYNAMIC_DRAW`|Data changed frequently, used many times.|

> [!tip]  
> For static geometry like a triangle, use **`GL_STATIC_DRAW`** — optimized for long-term, read-heavy data.  
> For constantly changing meshes or particles, use **`GL_DYNAMIC_DRAW`**.

---

### Step 6 — Summary of the Process

> [!important]
> 
> 1. Define your **vertex data** in normalized device coordinates.
>     
> 2. Generate a **VBO** with `glGenBuffers`.
>     
> 3. Bind it to `GL_ARRAY_BUFFER` with `glBindBuffer`.
>     
> 4. Upload your vertex data to GPU memory with `glBufferData`.
>     
> 5. The data now lives on the GPU, ready for use by the **vertex shader**.
>     

---

### Full Example Context

```cpp
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};

unsigned int VBO;
glGenBuffers(1, &VBO);                                    // 1. Generate buffer
glBindBuffer(GL_ARRAY_BUFFER, VBO);                       // 2. Bind to array buffer target
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices,  // 3. Upload vertex data
             GL_STATIC_DRAW);
```

> [!note]  
> At this point, your vertex data resides in GPU memory.  
> Next, you’ll write and link **vertex and fragment shaders** to process and display these vertices on screen.