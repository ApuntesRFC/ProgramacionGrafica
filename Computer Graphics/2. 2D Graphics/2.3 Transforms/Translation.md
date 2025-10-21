
> [!summary]
**Translation** is the simplest geometric transformation: it moves every point of a shape by a fixed amount horizontally and vertically.  
It shifts the entire object’s position without changing its size, orientation, or shape.

---

### Definition

If $(x, y)$ is the original point and $(x', y')$ the transformed point, the translation is:

$$
\begin{cases}
x' = x + e \\
y' = y + f
\end{cases}
$$

where:

> - $e$ = horizontal displacement (positive → right)  
> - $f$ = vertical displacement (positive → upward)

In affine transformation form:

$$
T(x, y) = (a x + b y + e,\; c x + d y + f)
$$
For a **pure translation**:
$$
a = d = 1, \quad b = c = 0
$$

---

### Translation in Graphics Systems

Most 2D graphics APIs include a translation function:

```text
translate(e, f)
````

> [!info]
> 
> - The translation affects **all subsequent drawing operations**.
>     
> - Once applied, every point drawn afterward has $e$ added to its x-coordinate and $f$ to its y-coordinate.
>     
> - The current coordinate system itself is shifted.
>     

---

### Example

Suppose you draw the letter **F** centered at the origin (0,0).  
Then you call `translate(4, 2)` before drawing:

> [!example]
> 
> - Every vertex of the “F” moves by (4, 2).
>     
> - The “F” becomes centered at (4, 2) instead of (0, 0).
>     
> - Its shape and orientation remain unchanged.
>     

> [!note]  
> The origin (0,0) is typically at the **bottom-left** in graphics coordinates.  
> Increasing _y_ moves **upward**, and increasing _x_ moves **rightward**.

---

### Combining Translations

Translations **accumulate** rather than replace each other.

> [!example]
> 
> - After `translate(5, 3)` → then `translate(-2, 1)`
>     
> - The combined result is equivalent to `translate(3, 4)`.
>     

> [!tip]  
> Every transformation modifies the **current transformation matrix (CTM)**.  
> New transformations multiply with existing ones rather than resetting them.

---

### Key Idea

You don’t manually compute new coordinates after transformation.  
Instead:

> - Define objects using **natural coordinates** (e.g., centered at origin).
>     
> - Apply transformations such as `translate(e, f)`.
>     
> - The system automatically adjusts coordinates during rendering.
>     

---

### Summary Table

|Concept|Description|
|---|---|
|Transformation type|Translation|
|Formula|$(x', y') = (x + e, y + f)$|
|Parameters|$e$: shift in x, $f$: shift in y|
|Geometric effect|Moves object without rotation or scaling|
|Composition|Translations add: $(e_1 + e_2, f_1 + f_2)$|
|Affine matrix|$\begin{bmatrix}1 & 0 & e \ 0 & 1 & f \ 0 & 0 & 1\end{bmatrix}$|

---

### Visualization Idea

A coordinate translation is equivalent to **moving the coordinate grid** itself:

> [!tip]  
> Translating the coordinate system right by 4 and up by 2  
> has the same visual effect as moving every object left by 4 and down by 2.

Thus, transformations can be interpreted as **modifying the drawing space** or **moving the objects** — both are equivalent mathematically.