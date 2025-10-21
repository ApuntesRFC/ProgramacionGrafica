
> [!summary]
In 2D computer graphics, **coordinate transformations** connect the abstract world of geometric objects with the pixel-based display.  
They convert positions from **world coordinates** (real numbers) to **viewport coordinates** (pixels).

---

### Viewport

The **viewport** is the rectangular region on the screen — made of pixels — where the image is displayed.  
It defines the visible area in **device coordinates** (e.g., 800×600 pixels).

> [!note]
> - The viewport corresponds to the **drawing surface** or **canvas**.  
> - Each point inside it has integer pixel coordinates.  
> - Example: `(0,0)` is usually the bottom-left or top-left corner, depending on the system.

---

### World Coordinates and the Window

Objects are usually defined in a separate coordinate system — the **world coordinate system** — which uses **real numbers**.

> [!info]
> - The **window** is a rectangular region in world coordinates that determines what part of the scene will be shown.  
> - A **coordinate transformation** maps the window to the viewport.

Example transformation:
$$
T(x, y) = (800 \cdot (x + 4)/8,\; 600 \cdot (3 - y)/6)
$$

This maps world coordinates $(x, y)$ in the window $[-4, 4] \times [-3, 3]$  
to pixel coordinates in a viewport of size $800 \times 600$.

> [!tip]
> The window defines **what** part of the world you see.  
> The viewport defines **where** on the screen you see it.

---

### Why Use Multiple Coordinate Systems

Choosing natural coordinates makes it easier to describe and reuse objects.

> [!example]
> - You define an object once in its **object coordinates** (e.g., a car centered at the origin).  
> - You then apply a **modeling transformation** to place it into **world coordinates**.  
> - Finally, a **view transformation** maps the world coordinates to the **viewport**.

This allows you to:
> - Place multiple copies of the same object at different positions.  
> - Animate motion, rotation, or scaling easily.  
> - Separate logical definition (object) from visual placement (scene).

---

### Modeling vs. Viewing Transformations

> [!info]
> - **Modeling transformation:** converts object coordinates → world coordinates.  
> - **Viewing transformation:** converts world coordinates → viewport (pixel) coordinates.  
> - Combined, they determine how each object appears on screen.

> [!example]
> Moving or resizing the window has the same effect as **moving or scaling the scene** in the opposite direction.  
> The viewport acts like a **camera** — shifting or zooming it changes the perceived position and size of objects.

**Interactive demo:**  
[Transform Equivalence — 2D](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/transform-equivalence-2d.html)

---

### Mathematical View: Coordinate Transformations

Geometric primitives are specified in natural coordinates.  
The computer applies a sequence of transformations to map them to the coordinates used for drawing.

> [!note]
> Although we distinguish between “modeling” and “viewing” transformations, mathematically they are the same:  
> both are **affine transformations** applied to geometric data.

---

### General Form of 2D Transformation

A 2D transformation maps $(x, y)$ to $(x', y')$ as:

$$
\begin{cases}
x' = a \cdot x + b \cdot y + e \\
y' = c \cdot x + d \cdot y + f
\end{cases}
$$

or in compact vector form:

$$
T(x, y) = (a x + b y + e,\; c x + d y + f)
$$

> [!tip]
> The transformation is fully defined by **six constants**: $a, b, c, d, e, f$.

---

### Affine Transformation

A transformation of the above form is called an **affine transformation**.

> [!info]
> - Preserves straight lines.  
> - Keeps parallel lines parallel.  
> - Includes translation, scaling, rotation, reflection, and shear.  
> - The composition of affine transformations is **another affine transformation**.

---

### Key Idea

The 2D graphics pipeline relies on a hierarchy of **coordinate systems**:

| Stage | Coordinates | Transformation |
|--------|--------------|----------------|
| Object | Local model | Modeling |
| World | Global scene | Viewing |
| Viewport | Pixel space | Device mapping |

Together, these transformations allow you to define scenes logically, manipulate them geometrically, and render them accurately on screen.

