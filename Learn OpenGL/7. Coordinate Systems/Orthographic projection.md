> [!summary]  
> **Orthographic projection** creates a cube-shaped frustum where all objects inside are visible **without perspective distortion**.  
> Parallel lines remain parallel, and object size does **not change with distance**.

---

### 1. Concept

- Defines a **rectangular viewing box (frustum)**.
    
- Objects within this box are visible; anything outside is clipped.
    
- No depth-based scaling — distant objects appear the **same size** as nearby ones.
    
- Used for **2D rendering**, **UI**, **CAD**, or **isometric** scenes.
    

---

### 2. Frustum Definition

The orthographic frustum is defined by:

- **Left (l)** and **Right (r)** → horizontal bounds
    
- **Bottom (b)** and **Top (t)** → vertical bounds
    
- **Near (n)** and **Far (f)** → depth range
    

All points within this 3D box map directly to **Normalized Device Coordinates (NDC)** in the range [-1, 1].

---

### 3. GLM Example

```cpp
glm::mat4 projection = glm::ortho(
    0.0f, 800.0f,   // left, right
    0.0f, 600.0f,   // bottom, top
    0.1f, 100.0f    // near, far
);
```

This maps a region from `(0, 0)` to `(800, 600)` directly to the viewport — perfect for 2D screen space.

---

### 4. Mathematical Form

The orthographic projection matrix:

$$  
P_{ortho} =  
\begin{bmatrix}  
\frac{2}{r-l} & 0 & 0 & -\frac{r+l}{r-l} \  
0 & \frac{2}{t-b} & 0 & -\frac{t+b}{t-b} \  
0 & 0 & -\frac{2}{f-n} & -\frac{f+n}{f-n} \  
0 & 0 & 0 & 1  
\end{bmatrix}  
$$

---

> [!info]
> 
> - Orthographic projection does **not** modify the **w** component (remains 1.0).
>     
> - Therefore, **perspective division** has no effect — distances don’t affect size.
>     
> - Great for HUDs, 2D overlays, or strategic games.
>     
> - For realistic depth and perspective, use a **perspective projection matrix** instead.
>