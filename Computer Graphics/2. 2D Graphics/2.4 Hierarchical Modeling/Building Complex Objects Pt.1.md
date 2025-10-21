
> [!summary]
**Hierarchical modeling** builds complex objects from simpler components using transformations.  
Each part uses its own local coordinate system, combined through scaling, rotation, and translation.

---

### Concept: Hierarchical Structure

> [!info]
> - Complex models are composed of **simpler sub-objects**.  
> - Each sub-object can itself be built from **geometric primitives** (lines, circles, polygons).  
> - The hierarchy defines how these parts are **positioned, oriented, and scaled** relative to one another.

> [!tip]
> This approach mirrors real-world structure — e.g., a car has wheels, doors, and body; each wheel has a rim and tire.

---

### Role of Transformations

Each component is defined in its **natural coordinate system**, typically centered at the origin `(0,0)` or a convenient reference point.

To place it in the final scene:
1. **Scale** — sets the size.  
2. **Rotate** — sets the orientation.  
3. **Translate** — sets the position.

> [!note]
> The usual order is **scale → rotate → translate** because:
> - Scaling and rotation keep the reference point fixed.  
> - Translation moves the object afterward, placing it correctly.

> [!important]
> In **code**, transformations are written in **reverse order** because of matrix multiplication order.

---

### Modeling Transformations

A **modeling transformation** converts an object’s *local coordinates* (natural coordinates) into *world coordinates* (scene coordinates).

> [!example]
> You might design a tree centered at (0,0).  
> - Apply `scale(2, 2)` → enlarge it.  
> - Apply `rotate(30°)` → tilt it.  
> - Apply `translate(10, 5)` → position it in the scene.

The final tree is now placed correctly in the world, independent from other objects.

---

### Isolating Transformations

To ensure modeling transformations of one object **don’t affect others**, use **save** and **restore** of the transformation state.

#### Pseudocode:
```text
saveTransform()
translate(dx, dy)   // move object into position
rotate(r)           // set orientation
scale(sx, sy)       // set size
...                 // draw the object in its local coordinates
restoreTransform()
````

> [!note]
> 
> - `saveTransform()` stores the current transformation matrix (CTM).
>     
> - `restoreTransform()` resets it to the previous state.
>     
> - Some APIs call these `pushMatrix()` and `popMatrix()`.
>     

---

### Modeling Transform and Coordinate Transform

> [!info]
> 
> - The **modeling transform** moves an object from its local space to its correct place in the scene.
>     
> - The **coordinate transform** (like the window-to-viewport transform) further maps it to display coordinates.
>     

The two multiply together:  
$$  
\text{Final Position} = T_{\text{coordinate}} \times T_{\text{modeling}} \times v  
$$

> [!tip]  
> You can think of the modeling transform as placing the object _in the world_, and the coordinate transform as taking the _world to the screen_.

---

### Key Advantages of Hierarchical Modeling

> [!note]
> 
> - Simplifies **complex scenes** — transformations are relative.
>     
> - Allows **reusability** — the same base shape can be placed multiple times.
>     
> - Enables **articulated motion** — e.g., moving arms, rotating wheels.
>     
> - Keeps the system **modular and manageable**.
>     

---

**Continue to → [[Building Complex Objects Pt. 2 |Part 2]]**