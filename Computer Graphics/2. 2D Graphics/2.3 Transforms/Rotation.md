
> [!summary]
A **rotation transformation** turns every point in a shape around the **origin** by a specified angle.  
All points are rotated by the same angle, preserving distances and shape.

---

### Definition

Rotation transforms a point $(x, y)$ into $(x', y')$ by rotating it around the origin by an angle $r$:

$$
\begin{cases}
x' = \cos(r) \cdot x - \sin(r) \cdot y \\
y' = \sin(r) \cdot x + \cos(r) \cdot y
\end{cases}
$$

> [!note]
> - $r$ is the **angle of rotation**.  
> - Units depend on the API:  
>   > - Java 2D and JavaScript → **radians**  
>   > - OpenGL and SVG → **degrees**

---

### Direction of Rotation

> [!info]
> - **Positive angles** rotate from the positive x-axis toward the positive y-axis.  
> - This is **counterclockwise** when the y-axis points upward.  
> - In **pixel coordinates** (y increasing downward), it appears **clockwise**.

> [!tip]
> The direction depends on how the coordinate system is defined — screen coordinates invert the y-axis.

---

### Rotation as an Affine Transformation

In affine form:
$$
T(x, y) = (a x + b y + e,\; c x + d y + f)
$$

For pure rotation about the origin:
$$
a = d = \cos(r), \quad b = -\sin(r), \quad c = \sin(r), \quad e = f = 0
$$

Or, equivalently, in **matrix form**:
$$
\begin{bmatrix}
x' \\
y'
\end{bmatrix}
=
\begin{bmatrix}
\cos(r) & -\sin(r) \\
\sin(r) & \cos(r)
\end{bmatrix}
\begin{bmatrix}
x \\
y
\end{bmatrix}
$$

---

### Usage in Graphics APIs

Most 2D graphics APIs include a function such as:

```text
rotate(r)
````

> [!note]
> 
> - The rotation affects **all subsequent drawing operations**.
>     
> - Any shape drawn after `rotate(r)` will appear rotated by angle $r$.
>     
> - The rotation is always **around the origin (0,0)** unless combined with a translation.
>     

> [!example]  
> Suppose you draw a square centered at (0,0).  
> Calling `rotate(45°)` before drawing rotates the square 45 degrees around the origin.

---

### Combining Transformations

Rotations combine **multiplicatively** when applied sequentially:

> [!example]
> 
> - `rotate(30°)` followed by `rotate(45°)` → total rotation `rotate(75°)`.
>     
> - `rotate(90°)` followed by `rotate(-90°)` → returns to original orientation.
>     

> [!tip]  
> To rotate around a point $(x_c, y_c)$ other than the origin:
> 
> 1. Translate the object by `(-x_c, -y_c)`
>     
> 2. Apply `rotate(r)`
>     
> 3. Translate back by `(x_c, y_c)`
>     

---

### Key Idea

Rotation preserves:

> - Shape and size (distances remain constant)
>     
> - Angles between lines
>     
> - Orientation (unless mirrored)
>     

It changes only the **direction** of points relative to the origin — forming the foundation for geometric transformations in graphics and animation.
