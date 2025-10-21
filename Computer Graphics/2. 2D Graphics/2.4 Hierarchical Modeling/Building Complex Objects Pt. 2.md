
> [!summary]
**Hierarchical modeling** allows building complex objects by composing smaller components, each defined in its own local coordinate system.  
Every level of the hierarchy has its own modeling transformations that position, scale, and rotate its subparts.

---

### Recap from [[Building Complex Objects Pt.1 | Part 1]]

Previously, we saw how to use **modeling transformations** (`translate`, `rotate`, `scale`) to place a single object in the scene.  
Now, we extend that concept: what if an object itself is **made of multiple smaller objects**?

---

### Complex Objects as Compositions

> [!info]
> A complex object (like a car, robot, or windmill) is made up of **sub-objects** — each one a smaller model with its own coordinate system and geometry.

Each sub-object:
> - Is defined in **its own natural coordinates**.  
> - Is **placed** into the main object using a **modeling transformation**.

The main object, in turn, has its **own coordinate system** — as if it were a scene.

> [!example]
> Think of a **windmill**:  
> - The base, body, and blades each have their own coordinate systems.  
> - Each blade is rotated and translated into place relative to the hub.  
> - Then, the entire windmill can be rotated, scaled, or moved as a whole.

---

### Transformation Hierarchy

When an object contains sub-objects, their transformations **combine** hierarchically:

$$
T_{\text{total}} = T_{\text{scene}} \times T_{\text{main}} \times T_{\text{subobject}}
$$

> [!note]
> The sub-object is first positioned within its parent object,  
> then the parent is positioned within the scene.

---

### Pseudocode Example

> [!example]
> Suppose we want to draw a main object composed of two sub-objects, each placed differently.

```text
saveTransform()

// Transformation for the first sub-object
translate(2, 1)
rotate(45)
scale(0.5, 0.5)
drawSubObject()

// Transformation for the second sub-object
restoreTransform()
saveTransform()
translate(-1, 2)
rotate(-30)
scale(0.7, 0.7)
drawSubObject()

restoreTransform()
````

> [!tip]  
> The reason both sub-objects appear in different positions  
> is that **different modeling transformations** were active during each subroutine call.

---

### Hierarchical Modeling Structure

> [!info]  
> This method can be extended to **any number of levels**:
> 
> - Sub-objects can contain **smaller sub-objects**,
>     
> - Each with its **own coordinate system** and transformations.
>     

For example:

- A **robot** → arms → forearms → hands → fingers.
    
- Transforming the arm moves the hand and fingers along automatically.
    

---

### Applying Transformations to the Whole Object

After defining the sub-objects and their relations,  
you can apply a **global transformation** to the entire complex object —  
all parts move together as one entity.

> [!example]  
> You can draw multiple instances of the same complex object by simply applying different modeling transformations before calling its draw function:
> 
> ```text
> translate(10, 0)
> drawWindmill()
> 
> translate(-10, 0)
> drawWindmill()
> ```
> 
> Both windmills will be identical but placed in different positions.

---

### Analogy: Subroutines in Programming

Hierarchical modeling is like **modular programming**:

- Each sub-object is like a **function**.
    
- Each level of the hierarchy encapsulates transformations and details.
    
- The scene becomes a tree of transformations and objects.
    

---

### Key Takeaways

> [!note]
> 
> - Each component has its **own coordinate system**.
>     
> - Modeling transformations **nest hierarchically**.
>     
> - The final transformation for any point is the **product of all transformations** up the hierarchy.
>     
> - Using `saveTransform()` and `restoreTransform()` ensures isolation between sub-objects.
>     

---

> [!example]  
> See an interactive visualization:  
> [**Cart and Windmills Demo**](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/cart-and-windmills.html)

^353eee

Three identical windmills are drawn using the same subroutine but with **different transformations**, demonstrating hierarchical modeling in action. ^3a95a9

---

**Continue to → [[Building Complex Objects Pt. 3 | Part 3]]**
