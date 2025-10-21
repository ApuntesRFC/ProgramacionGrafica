
> [!summary]
No graphics API can include **every possible shape** as a built-in primitive.  
Instead, complex shapes like **polygons**, **paths**, and **curves** are built from simpler commands and primitives.

---

### Polygons

A **polygon** is a closed shape made up of a sequence of connected line segments.  
Each line connects two **vertices**, and the polygon is defined by listing its vertices in order.

> [!info]
> - **Regular polygon:** all sides equal and all interior angles equal.  
> - **Convex polygon:** for any two points inside, the connecting line stays entirely inside (no indentations).  
> - **Non-convex polygon:** has indentations; some connecting lines pass outside.  
> - **Simple polygon:** no self-intersections; sides only meet at shared endpoints.  
> - **Planar polygon:** all vertices lie on the same plane (automatic in 2D, essential in 3D).

> [!note]
> In 3D graphics, **non-planar polygons** cause rendering artifacts; usually polygons are required to be planar and triangulated.

---

### Drawing Polygons in Graphics APIs

A polygon is typically drawn by **stroking** (outlining) or **filling** (coloring) its interior.  
The vertices are usually passed as arrays:

> [!example]
> - **Array of points:** `[(x1, y1), (x2, y2), (x3, y3)]`  
> - **Separate coordinate arrays:** `x[] = {...}`, `y[] = {...}`  
>  
> Example in Java’s Graphics API:  
> `drawPolygon(int[] xPoints, int[] yPoints, int nPoints);`  
> `fillPolygon(int[] xPoints, int[] yPoints, int nPoints);`

---

### Paths

A **path** is a more general shape concept. It can include both straight and curved segments, connected or not.

> [!info]
> Paths are used in **Java 2D**, **SVG**, and **HTML Canvas**.  
> A path is constructed by issuing commands that move a “virtual pen” around the canvas.

Common commands:

| Command | Description |
|----------|--------------|
| `createPath()` | Start a new empty path. |
| `moveTo(x, y)` | Move the pen to `(x, y)` without drawing (start new subpath). |
| `lineTo(x, y)` | Draw a line from current position to `(x, y)`. |
| `closePath()` | Connect current point back to the start of the subpath. |

> [!note]
> - A **subpath** is a series of connected segments started by a `moveTo`.  
> - A `closePath` ends the current subpath and starts a new one automatically.  
> - The **starting point** refers to the location after the most recent `moveTo` or `closePath`.

---

### Example — Triangle Path

To create a triangle with vertices at (100, 100), (300, 100), and (200, 200):

```text
createPath()
moveTo(100, 100)
lineTo(300, 100)
lineTo(200, 200)
closePath()
````

> [!note]  
> Creating a path defines geometry, not visibility.  
> You must **stroke** or **fill** the path to make it visible on screen.

---

### Example — Oval Path

It’s better to define an **oval** as a path than as separate line segments.  
This allows smoother joins and control over fill and stroke behavior.

```
createPath()
moveTo(x + r1, y)
for i = 1 to numberOfPoints-1:
    angle = i * (2 * pi / numberOfPoints)
    lineTo(x + r1 * cos(angle), y + r2 * sin(angle))
closePath()
```

> [!tip]  
> Defining an oval as one continuous path lets the renderer treat it as a single object, ensuring smoother outlines and consistent fill rules.

---

### Arcs and Curves in Paths

A path can also contain **arcs** (circular segments) and **Bézier curves**, enabling the creation of smooth, flowing shapes.

---

### Bézier Curves

A **Bézier curve** is a mathematically defined curve that’s easy to control interactively.

Defined by **parametric polynomial equations**.  
The curve is tangent to the lines connecting the **control points** and endpoints.

> [!note]
> 
> - **Cubic Bézier curve:** has 2 control points.
>     
> - **Quadratic Bézier curve:** has 1 control point.
>     
> - The curve always starts at the current pen position and ends at the specified endpoint.
>     

---

### Cubic Bézier Example

Command:  
`cubicCurveTo(cx1, cy1, cx2, cy2, x, y)`

> [!example]
> 
> - Uses the current pen position as the first endpoint.
>     
> - `(cx1, cy1)` and `(cx2, cy2)` define the control points.
>     
> - `(x, y)` is the second endpoint.
>     
> - The curve is tangent to the line through the first endpoint and `(cx1, cy1)` at the start, and tangent to the line through `(cx2, cy2)` and `(x, y)` at the end.
>     

> [!tip]  
> The control points “pull” the curve toward them.  
> The more distant a control point, the stronger its influence on curvature.

>!**Interactive example:**  
[Cubic Bézier Demo](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/cubic-bezier.html)

---

### Quadratic Bézier Example

Similar, but with only one control point:

Command:  
`quadraticCurveTo(cx, cy, x, y)`

> [!example]  
> The curve starts at the current pen location, bends toward the control point `(cx, cy)`, and ends at `(x, y)`.  
> The result is an **arc of a parabola**.

**Interactive example:**  
[Quadratic Bézier Demo](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/quadratic-bezier.html)

---

### Why Paths Are Preferred

> [!important]
> 
> - Paths treat all connected segments (lines, arcs, curves) as **one shape**.
>     
> - This ensures consistent stroking, filling, and join styles.
>     
> - You can combine multiple shapes into a single path to manage fill rules and appearances uniformly.
>     

---

### Key Idea

Graphics APIs build **complex shapes** from a minimal set of drawing commands.  
Polygons, paths, and Bézier curves provide flexibility to define any form — from triangles to smooth freehand curves — while stroking and filling determine their visual representation.