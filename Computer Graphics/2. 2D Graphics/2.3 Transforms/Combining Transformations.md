> [!summary]
When **multiple transformations** are combined, **the order matters**.  
Each transformation modifies the coordinate system itself, so later transformations operate in the already-transformed space.

---

### Order of Application

In a graphics API, transformations are applied to **everything drawn after them**, not immediately to existing objects.

> [!note]
> - `translate(4, 0)` means: move the entire coordinate system 4 units right.  
> - `rotate(90)` means: rotate the coordinate system 90° around the origin.  
> - The commands modify the **current transformation matrix (CTM)**.

---

### Example 1: `translate(4, 0)` then `rotate(90)`

> [!example]
> Step-by-step:
> 1. `translate(4, 0)` — shifts the coordinate system **4 units right**.  
> 2. `rotate(90)` — rotates the **entire coordinate system**, including that translation, **counterclockwise by 90°**.

So the translation you made (to the right) is also **rotated upward** by 90°.  
After these two transforms, objects drawn at (0,0) appear at **(0, 4)** — not (4, 0).

> [!tip]
> Transforms are **cumulative**: every new one multiplies the previous ones.  
> The later a transformation is written, the **earlier it applies geometrically**.

---

### Example 2: `rotate(90)` then `translate(4, 0)`

Now:
> 1. `rotate(90)` — rotates the coordinate system first.  
> 2. `translate(4, 0)` — moves 4 units **to the right in the rotated system**,  
>    which, in world coordinates, means **4 units upward**.

Thus the final result is **different** from the first example.

| Order | Final displacement (world coordinates) |
|--------|----------------------------------------|
| `translate(4, 0)` → `rotate(90)` | up by 4 units |
| `rotate(90)` → `translate(4, 0)` | right by 4 units |

> [!important]
> Transformations are **not commutative** — changing the order changes the result.

---

### Why This Happens

Each transformation updates the **current coordinate frame**:
> - After a `translate`, the origin moves.  
> - After a `rotate`, the x and y axes rotate.  
> - Any transformation afterward works **relative to the new frame**, not the original one.

> [!note]
> Think of it like moving and turning a camera:  
> If you move first then rotate, you end up facing a different direction than if you rotate first then move.

---

### Example 3: Rotate a Shape Around a Point $(p, q)$

To rotate **around a point that is not the origin**, you must:
1. Move the rotation point $(p, q)$ to the origin.  
2. Perform the rotation.  
3. Move it back.

Mathematically:
$$
T = \text{translate}(-p, -q) \cdot \text{rotate}(r) \cdot \text{translate}(p, q)
$$

> [!example]
> In code (executed top-down):
> ```text
> translate(p, q)
> rotate(r)
> translate(-p, -q)
> ```
> Although it looks reversed, the **last line executes first** geometrically —  
> the transformations are applied in the **opposite order** of how they appear in code.

> [!tip]
> Some APIs provide a shorthand:  
> `rotate(r, p, q)` — equivalent to the three-step sequence above.

---

### Key Idea

Transformations form a **stack of coordinate manipulations**.  
Each new transform multiplies the current one, modifying the entire drawing space.  

> [!important]
> The **later** the command appears in code, the **earlier** it acts on the geometry.

| Code Order | Geometric Order |
|-------------|----------------|
| First written | Last applied |
| Last written | First applied |

Understanding this order is critical for correctly positioning and orienting objects in 2D and 3D graphics.
