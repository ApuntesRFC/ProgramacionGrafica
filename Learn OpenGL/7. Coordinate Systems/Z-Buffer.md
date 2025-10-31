> [!summary]  
> **Depth testing** ensures correct visibility by comparing fragment depth values stored in the **z-buffer**.  
> Enable it once, and clear it every frame together with the color buffer.

---

### 1. What the Depth Buffer Does

- Every fragment stores a **depth value (z)**.
    
- OpenGL keeps a **z-buffer** (depth buffer) alongside the color buffer.
    
- When a new fragment is drawn:
    
    - If its depth < stored depth → **visible**, drawn and replaces the old one.
        
    - Otherwise → **discarded** (it’s behind something).
        

This process happens automatically after you enable depth testing.

---

### 2. Enabling and Using Depth Testing

```cpp
// Enable once during setup
glEnable(GL_DEPTH_TEST);
```

Each frame:

```cpp
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

> Failing to clear the depth buffer means new frames reuse old depth data, producing strange occlusion artifacts.

---

### 3. How It Works in the Pipeline

1. Vertex coordinates are transformed through **model → view → projection**.
    
2. The **z-component** of each fragment becomes its **depth value** in NDC (after perspective division).
    
3. The **depth test** compares this value against the stored one using the current **depth function** (default `GL_LESS`).
    

---

### 4. Optional: Depth Function and Clearing Rules

```cpp
// default comparison mode
glDepthFunc(GL_LESS);

// clear depth buffer to 1.0 (max depth)
glClearDepth(1.0f);
```

Depth values range from **0.0 (near plane)** to **1.0 (far plane)**.

---

> [!info]
> 
> - **Precision:** Depth buffer precision depends on your framebuffer format (commonly 24 bits).
>     
> - **Performance:** Keep `near` > 0.1 and `far` < 1000 to maintain precision and avoid z-fighting.
>     
> - **Skyboxes:** Use `glDepthFunc(GL_LEQUAL)` when drawing at maximum depth.
>     
> - **Transparency:** Transparent objects should be rendered **after** opaque ones, with depth testing still enabled but depth writing disabled (`glDepthMask(GL_FALSE)`).
>     
> - **Clearing:** Always clear both color and depth each frame:
>     
>     ```cpp
>     glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
>     ```

