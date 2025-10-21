
> [!summary]
The **window-to-viewport transform** maps a world-space rectangle (the *window*) onto a pixel rectangle (the *viewport*). It is just a **translate** then **scale** of coordinates; aspect ratio may require expanding the window.

---

### Goal and assumptions
- Window limits (world coords): `left, right, bottom, top` (axes parallel to x/y).
- Viewport size (pixels): `width × height`, with **top-left at (0,0)** and y increasing **down**.
- Map any point `(x, y)` in the window to pixel coords `(x₁, y₁)` in the viewport.

---

### Direct mapping formulas
Translate the window so its top-left goes to `(0,0)`, then scale to fill the viewport:
$$
x_1=\frac{\text{width}}{\text{right}-\text{left}}\,(x-\text{left}),\qquad
y_1=\frac{\text{height}}{\text{bottom}-\text{top}}\,(y-\text{top})
$$

> [!note]
> - `(left, top) → (0, 0)`  
> - `(right, bottom) →(width, height)`

---

### As transforms (geometric order)
1) **Translate** by `(-left, -top)`  
2) **Scale** by $(\dfrac{\text{width}}{\text{right} - \text{left}},\ \dfrac{\text{height}}{\text{bottom} - \text{top}})$

> [!tip]
> Many 2D APIs compose matrices in the opposite **code** order.  
> If your API applies the *last* written transform **first** geometrically, you will **code**:
> - `scale( width/(right-left), height/(bottom-top) )`
> - `translate( -left, -top )`

---

### Why aspect ratio matters
Let
$$
\text{viewAspect}=\frac{\text{height}}{\text{width}},\qquad
\text{winAspect}=\frac{\text{top}-\text{bottom}}{\text{right}-\text{left}}
$$
If `viewAspect ≠ winAspect`, the image distorts.  
Fix: **expand the window** (not the viewport) along the shorter direction until aspects match, then apply the mapping.

---

### Aspect-preserving adjustment (pseudocode)
```c++
applyWindowToViewportTransformation(
  left, right,     // window horizontal limits
  bottom, top,     // window vertical limits
  width, height,   // viewport size in pixels
  preserveAspect   // boolean
):
  if preserveAspect:
    displayAspect = abs(height / width)
    windowAspect  = abs((top - bottom) / (right - left))

    if displayAspect > windowAspect:
      // View is "taller" than window: expand window vertically
      scaleFactor = displayAspect / windowAspect
      excess = (top - bottom) * (scaleFactor - 1)
      top    = top    + excess/2
      bottom = bottom - excess/2
    else if displayAspect < windowAspect:
      // View is "wider" than window: expand window horizontally
      scaleFactor = windowAspect / displayAspect
      excess = (right - left) * (scaleFactor - 1)
      right  = right + excess/2
      left   = left  - excess/2

  // Geometric order: translate then scale
  // (Swap call order if your API applies them in reverse)
  translate(-left, -top)
  scale( width / (right - left), height / (bottom - top) )
```

---

### Minimal example (numbers)

- Window: `[-4, 4] × [-3, 3]`
    
- Viewport: `800 × 600`
    

Mapping:  
$$  
x_1=800\cdot\frac{x+4}{8},\qquad  
y_1=600\cdot\frac{y-3}{-6}=600\cdot\frac{3-y}{6}  
$$

This is exactly:

- `translate(-left, -top) = translate(4, -3)`
    
- `scale( 800/8, 600/(-6) ) = scale(100, -100)`
    

---

### Mental model

- **Translate** recenters the window to the viewport origin.
    
- **Scale** stretches it to the viewport size.
    
- If the **aspects differ**, first **expand** the window bounds in the shorter direction, then translate+scale.
    
- Code order vs geometric order depends on your API’s matrix composition; rely on the **explicit formulas** above if in doubt.