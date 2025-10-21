> [!summary]
There are two fundamental ways to make a shape visible in computer graphics: **stroking** its boundary or **filling** its interior.  
These operations define how geometry becomes visible.

---

### Stroking and Filling

> [!info]
> - **Stroking:** tracing the outline of a shape, like drawing with a pen along its path.  
> - **Filling:** coloring every point contained inside the shape’s area.  
> - You can apply both: stroke for the border and fill for the interior.

> [!example]
> A circle may have a **black stroke** and a **red fill** — two independent color operations.

---

### Self-Intersecting Shapes

When a shape crosses or loops over itself, the interior region becomes ambiguous.  
To determine what counts as “inside,” graphics systems use **fill rules**.

---

### Winding Number

The **winding number** counts how many times a shape’s boundary **wraps around a point**.

> [!note]
> - Each time the path winds **counterclockwise** around a point, the winding number **increases by 1**.  
> - Each time it winds **clockwise**, it **decreases by 1**.  
> - If the number ends up **zero**, the point lies outside.

---

### Two Common Fill Rules

> [!info]
> - **Non-zero winding rule:**  
>   > Fill every region whose winding number is **not zero**.  
>   > Even complex self-overlapping shapes appear solid.  
> - **Even–odd rule:**  
>   > Fill every region crossed an **odd** number of times by the boundary.  
>   > Regions with an even number of crossings are left unfilled (holes appear).

> [!tip]
> The even–odd rule is simpler but may create holes where loops overlap.  
> The non-zero rule preserves filled interiors for continuous winding.

---

### Fill Contents

A filled shape can contain different kinds of **color information**:

> [!info]
> - **Solid color:** one uniform color for all pixels inside.  
> - **Pattern:** a small image (tile) repeated across the area.  
> - **Gradient:** color that changes smoothly across the shape.

---

### Patterns

A **pattern** is a small bitmap or image that repeats both horizontally and vertically over the filled region.

> [!example]
> - Common patterns: stripes, checkerboards, textures.  
> - Each tile acts as a building block; when tiled, it gives the illusion of texture or repetition.

---

### Gradients

A **gradient** is computed by **interpolating color values** across space, producing a gradual change.

> [!info]
> - **Linear gradient:** color varies along a straight line between two points.  
>   > Example: from blue (left) to white (right).  
> - **Radial gradient:** color radiates outward from a center point.  
>   > Example: bright center fading into darker edges.

> [!tip]
> The gradient color at a pixel is calculated based on its **relative position** along the gradient vector (for linear) or distance from the center (for radial).

---

### Text as a Shape

Text glyphs are vector shapes — outlines that can be both **stroked and filled**.

> [!note]
> - **Stroking:** gives the text an outlined look.  
> - **Filling:** renders solid characters.  
> - Many systems use vector fonts (e.g., TrueType, OpenType) to allow scaling and transformation just like any other geometric shape.

---

### Key Idea

A shape becomes visible only when it has **stroke**, **fill**, or both.  
Fill rules resolve interior ambiguity, and the chosen **fill content** (solid, pattern, or gradient) defines the final appearance.
