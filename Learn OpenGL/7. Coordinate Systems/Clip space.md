> [!summary]  
> **Clip space** is the coordinate space where all visible vertices must fall within the range **[-1, 1]** in **x, y, and z**.  
> The **projection matrix** transforms **view space** coordinates into this range.

---

### 1. Purpose

- After applying the **model** and **view** matrices, vertices exist in **view space** (camera coordinates).
    
- OpenGL then expects them to be mapped into a **normalized cube** called the **clipping volume**, defined by:  
    $$  
    x, y, z \in [-1, 1]  
    $$
    
- Any vertex outside this cube is **clipped** (discarded).
    
- Triangles partially outside the range are **reconstructed** to fit within it.
    

---

### 2. The Projection Matrix

The **projection matrix** defines a _viewing volume_ — the region of space that becomes visible.

- It **maps world distances** (e.g. from -1000 to 1000)  
    → into **Normalized Device Coordinates (NDC)** between -1 and 1.
    
- This process is called **projection**, because it projects 3D points into a space that can be mapped to a 2D screen.
    

$$  
\text{ClipCoords} = \text{ProjectionMatrix} \cdot \text{ViewCoords}  
$$

---

### 3. Frustum and Perspective Division

- The projection matrix creates a **frustum**, a 3D pyramid-like volume that represents the camera’s visible space.
    
- After transformation, **perspective division** occurs automatically:
    

$$  
x_{ndc} = \frac{x_{clip}}{w}, \quad y_{ndc} = \frac{y_{clip}}{w}, \quad z_{ndc} = \frac{z_{clip}}{w}  
$$

This converts 4D clip coordinates into 3D **NDC**.  
Finally, NDC are mapped to **screen space** using `glViewport`.

---

### 4. Two Types of Projection

|Type|Description|Effect|
|---|---|---|
|**Orthographic**|Keeps parallel lines parallel|No depth perception|
|**Perspective**|Simulates camera lens|Distant objects appear smaller|

---

> [!info]
> 
> - Clip space defines what part of the world is **rendered**.
>     
> - The **projection matrix** determines the camera’s viewing volume.
>     
> - **Perspective division** ensures objects farther away appear smaller — giving true 3D perspective.
>     
> - The full transformation pipeline in OpenGL is:  
>     $$  
>     \text{gl\_Position} = 
>     
>     \text{projection} \times \text{view} \times \text{model} \times \vec{aPos}  
>     $$

