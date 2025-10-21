
> [!summary]
**Scaling transformations** resize objects by multiplying their coordinates by scaling factors along each axis.  
They enlarge, shrink, or mirror shapes depending on the scaling parameters.

---

### Definition

If $(x, y)$ is the original point and $(x', y')$ the transformed point, scaling is defined as:

$$
\begin{cases}
x' = a \cdot x \\
y' = d \cdot y
\end{cases}
$$

where:

> [!note]
> - $a$ = scaling factor along the **x-axis**  
> - $d$ = scaling factor along the **y-axis**

---

### Geometric Effect

> [!info]
> - The object **stretches** horizontally by $a$ and vertically by $d$.  
> - The center remains fixed if it is at the origin $(0,0)$.  
> - If the object is not centered at the origin, scaling moves it **away from** or **toward** $(0,0)$.

> [!example]
> - If $a = d = 2$: the object doubles in size.  
> - If $a = 0.5$, $d = 0.5$: the object shrinks to half its size.  
> - If $a > 1$, $d < 1$: the object stretches horizontally and compresses vertically.

---

### Uniform and Non-Uniform Scaling

> [!info]
> - **Uniform scaling:** $a = d$ → no distortion; proportions are preserved.  
> - **Non-uniform scaling:** $a \ne d$ → object stretches more along one axis.

> [!tip]
> Uniform scaling preserves **angles** and **aspect ratios**, though not absolute lengths.

---

### Center of Scaling

Scaling is performed **relative to the origin (0,0)** by default.

> [!note]
> - Points farther from the origin move more after scaling.  
> - Scaling “pushes” or “pulls” all points toward or away from (0,0).

If you want to scale around another point $(p, q)$, you must:

> [!example]
> 1. Move $(p, q)$ to the origin → `translate(-p, -q)`  
> 2. Apply scaling → `scale(a, d)`  
> 3. Move it back → `translate(p, q)`

In code order:
```text
translate(p, q)
scale(a, d)
translate(-p, -q)
````

> [!tip]  
> This sequence ensures the chosen point stays fixed during scaling.

---

### Reflections

Negative scaling factors **reflect** the object across an axis.

> [!example]
> 
> - `scale(1, -1)` → reflection across the x-axis.
>     
> - `scale(-1, 1)` → reflection across the y-axis.
>     
> - `scale(-1, -1)` → rotation by 180° (reflection across both axes).
>     

---

### Implementation in Graphics APIs

Most 2D graphics systems provide:

```text
scale(a, d)
```

> [!info]
> 
> - Applies scaling to all coordinates of subsequent drawing operations.
>     
> - Affects every vertex, edge, and curve drawn after the command.
>     
> - Combines with any previously active transformations (translation, rotation, etc.).
>     

---

### Composition and Affine Transforms

Every **affine transformation** can be created by combining:

> - Translation
>     
> - Rotation
>     
> - Scaling
>     

> [!note]  
> Scaling changes **size**, while rotation changes **orientation**, and translation changes **position**.

---

### Preservation Properties

|Type of Transform|Lengths|Angles|Aspect Ratios|
|---|---|---|---|
|Translation|✔|✔|✔|
|Rotation|✔|✔|✔|
|Uniform Scaling|✖|✔|✔|
|Non-uniform Scaling|✖|✖|✖|

> [!tip]  
> **Euclidean transformations** (rotation + translation) preserve both distances and angles.  
> Adding uniform scaling preserves **angles and shape ratios**, but alters **size**.

---

### Interactive Demo

Explore how scaling, translation, and rotation combine in 2D:  
[2D Transformation Demo](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/transforms-2d.html)