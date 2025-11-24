> [!summary]  
> **Perspective projection** mimics how human vision works: distant objects appear smaller, and parallel lines seem to converge at a distance.  
> It’s implemented using a **perspective projection matrix**, which scales vertex coordinates based on their depth.

---

### 1. Concept

- Creates a **pyramid-shaped frustum** (not a box).
    
- Objects near the camera appear larger; distant ones appear smaller.
    
- Achieved by manipulating the **w component** of clip-space coordinates.
    

---

### 2. How It Works

After applying the projection matrix:

$$  
(x_{clip}, y_{clip}, z_{clip}, w_{clip})  
$$

OpenGL performs **perspective division**:

$$  
x_{ndc} = \frac{x_{clip}}{w_{clip}}, \quad  
y_{ndc} = \frac{y_{clip}}{w_{clip}}, \quad  
z_{ndc} = \frac{z_{clip}}{w_{clip}}  
$$

> As distance increases, $w$ becomes larger, shrinking $x$ and $y$ — this creates **depth-based size reduction**.

---

### 3. GLM Example

```cpp
glm::mat4 proj = glm::perspective(
    glm::radians(45.0f),      // Field of View (FOV)
    (float)width / height,    // Aspect ratio
    0.1f,                     // Near plane
    100.0f                    // Far plane
);
```

Parameters:

- **FOV** — controls how wide the camera’s vision is.
    
    - Typical: `45°` (natural view).
        
    - Larger FOV = more distortion (fisheye effect).
        
- **Aspect ratio** — viewport width ÷ height.
    
- **Near/Far planes** — define visible depth.
    
    - Objects outside this range are clipped.
        
    - Setting the **near plane too high** (e.g., 10.0) can cause **objects close to disappear**.
        

---

### 4. Visual Comparison

|Projection Type|Appearance|Use Cases|
|---|---|---|
|**Perspective**|Objects shrink with distance|Realistic 3D scenes, games|
|**Orthographic**|All objects same size regardless of distance|2D, CAD, modeling tools|

---

### 5. Mathematical Insight

The **perspective matrix** introduces non-zero terms in the last row and column to scale by depth:

$$  
P_{persp} =  
\begin{bmatrix}  
\frac{1}{a \tan(\frac{fov}{2})} & 0 & 0 & 0 \\  
0 & \frac{1}{\tan(\frac{fov}{2})} & 0 & 0 \\  
0 & 0 & -\frac{f+n}{f-n} & -\frac{2fn}{f-n} \\  
0 & 0 & -1 & 0  
\end{bmatrix}  
$$

---

> [!info]
> 
> - **Perspective projection** introduces realism by applying **depth scaling** through the w-component.
>     
> - **Orthographic projection** skips this scaling — useful when exact proportions matter.
>     
> - The full transformation chain is:  
>     $$  
>     \mathrm{gl_Position} = 
>     
>    \mathrm{projection}\times\mathrm{view}\times\mathrm{model}\times\vec{aPos}  
>     $$

