
> [!summary]
Basic drawing in graphics APIs depends on the set of **primitive shapes** and how many **commands** are required. Fewer commands usually mean simpler shapes. What counts as “basic” varies by API.

---

### What qualifies as a basic shape

Different graphics APIs expose different primitive sets and draw calls:

> [!info]
> - **WebGL (OpenGL ES 2.0–3.0 model):**
>   > - Primitives: `POINTS`, `LINES`, `LINE_STRIP`, `TRIANGLES`, `TRIANGLE_STRIP`, `TRIANGLE_FAN`.
>   > - Typical calls: `gl.createBuffer`, `gl.bindBuffer`, `gl.bufferData`, `gl.vertexAttribPointer`, `gl.enableVertexAttribArray`, `gl.drawArrays` / `gl.drawElements`.
>   > - No high-level rectangle or circle commands; compose with triangles or lines.
> - **OpenGL (modern core):**
>   > - Primitives: same families (`GL_POINTS`, `GL_LINES`, `GL_TRIANGLES`, …).
>   > - Typical calls: create VAO/VBO, set vertex formats, `glDrawArrays` / `glDrawElements`.
>   > - Legacy immediate mode (`glBegin/glEnd`) is deprecated.
> - **Vulkan:**
>   > - Low-level control via pipelines and command buffers.
>   > - Typical: create graphics pipeline, record `vkCmdBindPipeline`, `vkCmdBindVertexBuffers`, `vkCmdDraw` into a command buffer, then submit.
>   > - No built-in rectangles or circles; everything is triangles or lines.

---

### Our basic set

For our purposes, **lines, rectangles, and ovals** are the baseline shapes.

---

### Lines

A **line** is the segment connecting two points. At the lowest level it is rasterized into pixels.

> [!note]
> - The naïve “most basic” line can be a **1-pixel wide** segment **without antialiasing**.

**Bresenham’s line algorithm (overview):**  
Efficient integer algorithm that steps along the **major axis** (x or y) and uses an **error accumulator** to decide when to step along the minor axis, producing a close approximation to the ideal line without floating-point math.

> [!tip]
> - Core idea:
>   > - Choose the driving axis (the one with larger delta).
>   > - Increment one pixel per step on the driving axis.
>   > - Keep an integer “error” term; when it exceeds a threshold, step one pixel on the other axis and adjust the error.
> - Benefits: integer math, fast, minimal branches.

**Complications with lines:**
- **Antialiasing:** smooths “jaggies” by blending edge pixels (e.g., Xiaolin Wu’s algorithm or shader-based smoothing).
- **Line width:** GPU line width support is limited or deprecated; a robust approach is to render a **rectangle (quad)** oriented along the segment.
- **Caps and joins:** visual styling at segment ends and between segments.
  - **Caps:** butt/none, square, round.
  - **Joins:** miter, round, bevel.
  - **Patterns:** dashes and dots (historically line stipple; now usually shader-based with distance fields).

---

### Rectangles

A **basic rectangle** has horizontal and vertical sides.

Ways to specify:
- **Two points (diagonal endpoints):** `(x1, y1)` and `(x2, y2)`.
- **Origin + size:** one corner `(x, y)` plus `width`, `height`.  
  Which corner depends on the coordinate system (top-left vs bottom-left).

**Convert two points to origin+size (pseudocode):**
```text
DrawRectangleFromPoints(x1, y1, x2, y2):
  x = min(x1, x2)
  y = min(y1, y2)
  width  = abs(x1 - x2)
  height = abs(y1 - y2)
  DrawRectangle(x, y, width, height)
```

**Rounded corners:**  
Replace each corner with an **elliptical arc** of given radii; draw the four arcs plus four connecting straight segments (or triangulate a rounded-rect mesh).

---

### Ovals and Ellipses

An **oval/ellipse** has two radii: horizontal `r1` and vertical `r2`.  
A **circle** is the special case `r1 = r2`.

Parametric form for center `(cx, cy)`:

- $x(\theta) = c_x + r_1 \cdot \cos(\theta)$  
- $y(\theta) = c_y + r_2 \cdot \sin(\theta)$  
with $\theta \in [0, 2\pi]$.

If the API lacks native ellipses, approximate with **line segments**:

**Ellipse approximation (pseudocode):**

```text
DrawOval(cx, cy, r1, r2, N):
  for i = 0 to N-1:
    angle1 = i * (2*pi / N)
    angle2 = (i+1) * (2*pi / N)
    a1 = cx + r1 * cos(angle1)
    b1 = cy + r2 * sin(angle1)
    a2 = cx + r1 * cos(angle2)
    b2 = cy + r2 * sin(angle2)
    DrawLine(a1, b1, a2, b2)
```

> [!tip]
> 
> - Increase `N` for smoother curves.
>     
> - Ensure **aspect ratio** of the coordinate window matches the viewport to avoid unintended distortion.
>     

---

### Practical notes across APIs

> [!note]
> 
> - **No built-in high-level shapes:** Rectangles and circles are usually **meshes** (two triangles for a rect; fan/strip for circles).
>     
> - **Consistency:** Implement styling (caps, joins, dashes, thickness) in **shaders** for predictable cross-platform results.
>     
> - **Coordinates vs pixels:** Keep track of **coordinate aspect** vs **viewport aspect** to preserve geometry.
>