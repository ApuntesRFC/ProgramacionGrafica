
> [!summary]
**Animation** in computer graphics is the process of generating motion through a sequence of frames — each one a separate image where small changes occur from one to the next.  
Hierarchical modeling plays a key role in controlling how parts of an object move together.

---

### Basic Concept of Animation

> [!info]
> - A **computer animation** consists of many frames displayed in rapid succession.  
> - Each frame is effectively a **separate scene** with slightly different parameters.  
> - Animation = **modeling transformation changing over time**.

To animate an object, we simply modify its **modeling transformation** in each frame.

---

### Example: Simple Translation Animation

If we want a cart to move horizontally, we can use:

```text
translate(frameNumber * 0.1, 0)
````

Each frame advances the cart slightly to the right — the translation value depends on the current frame number.

> [!tip]  
> The change in transformation parameters per frame controls the **speed** and **direction** of motion.

---

### Example from [[Building Complex Objects Pt. 2#^353eee|the previous part]]

In the demo, the animation of the **cart** was implemented as:

```text
translate(-3 + 13 * (frameNumber % 300) / 300.0, 0)
```

This moves the reference point of the cart from **-3** to **13** every **300 frames**,  
creating a continuous looping motion.

> [!note]
> 
> - The coordinate system extends roughly from 0 to 7.
>     
> - Much of the cart’s path occurs outside the visible window.
>     

---

### Animation with Hierarchical Modeling

> [!info]  
> Animation works **seamlessly** with hierarchical modeling.  
> Each sub-object can have its own animation, and higher-level objects can be animated as a whole.

For example:

- The **windmill** object consists of sub-objects (body and vanes).
    
- Each vane can rotate independently as part of the windmill’s modeling transformation.
    
- Then, the **entire windmill** can be scaled, rotated, or translated as one unit.
    

This allows smooth, structured animations without manually computing all the coordinates each frame.

---

### Pseudocode Example

> [!example]  
> Example of animating a hierarchical model (a moving, rotating windmill):

```text
saveTransform()

// Move entire windmill across the scene
translate(-3 + 13 * (frameNumber % 300) / 300.0, 0)

// Draw animated sub-object (rotating vanes)
drawWindmill(frameNumber)

restoreTransform()
```

Inside `drawWindmill(frameNumber)`, the vanes may rotate based on time:

```text
rotate(frameNumber * 3)  // rotate vanes continuously
```

---

### Integration with Hierarchical Modeling

> [!note]
> 
> - Sub-objects (like vanes) have their **own animations**,  
>     which are still affected by the parent’s transformations.
>     
> - The **entire object** can be moved, scaled, or rotated as one.
>     
> - This mirrors how complex real-world motions (like a car or robot) are built from coordinated sub-motions.
>     

---

### Key Takeaways

> [!tip]
> 
> - Animation = updating transformation parameters per frame.
>     
> - Works naturally with **hierarchical models**.
>     
> - You can animate any component independently or as part of a larger structure.
>     
> - The **order of transformations** still matters — especially when animating rotations and translations.
>     

---

### Reference Implementations

- **Interactive demo** (animated windmills):  
    [HierarchicalModel2D Demo](https://math.hws.edu/eck/cs424/graphicsbook-1.4/source/canvas2d/HierarchicalModel2D.html)
    


