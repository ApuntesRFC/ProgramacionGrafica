> [!summary]
The coordinate system’s aspect ratio must match the drawing area’s aspect ratio to avoid distortion.  
If they differ, a circle like $x^2 + y^2 = 9$ will appear stretched or squashed.

---

### Coordinate System Aspect Ratio

The coordinate aspect ratio is defined as:
$$
\text{coordAspect} = \left|\frac{\text{right} - \text{left}}{\text{top} - \text{bottom}}\right|
$$

> [!note]
> - If `coordAspect ≠ viewAspect` (canvas width/height), shapes will be **stretched** or **compressed**.

---

### Fix: Extend the Shorter Axis to Match Ratios

Given $\,\text{viewAspect} = W/H\,$ and $x \in [\text{left}, \text{right}]$:  
$$
\text{desired y-size} = \frac{\text{right} - \text{left}}{\text{viewAspect}}
$$

Example for $W = 520$, $H = 300$, and $x \in [-4, 4]$:  
$$
\text{desired y-size} = \frac{8}{520/300} = \frac{8 \cdot 300}{520} \approx 4.615
$$
$$
y \in [-2.3075,\, 2.3075]
$$

> [!tip]
> Adjust the **shorter axis range** until `coordAspect = viewAspect` to preserve shape proportions and maintain consistent scale between axes.
