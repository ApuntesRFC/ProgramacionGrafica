
> [!summary]
Real-number coordinate systems describe positions using continuous values instead of discrete pixels, allowing precise modeling, scaling, and transformation of graphics.

---

### Real-Number Coordinate Systems

In **real-number coordinate systems**, positions are expressed using continuous real values rather than integer pixel indices.  
This provides flexibility and higher precision when defining shapes or performing transformations.

> [!note]
> - In **pixel coordinates**, each pixel is addressed by integer indices `(x, y)`.  
> - In **real-number coordinates**, `(x, y)` represents a **mathematical point** in a continuous plane, not bound to pixel centers.

---

### Thinking in Terms of Points

It is conceptually better to treat images as **continuous spaces filled with points** rather than arrays of pixels.  
For example, when defining a rectangle, coordinates specify the **edges** of the region:

> [!info]
> - Horizontal edges: left (`xmin`) and right (`xmax`)  
> - Vertical edges: bottom (`ymin`) and top (`ymax`)  
> - Some systems invert the Y-axis (e.g., screen coordinates), so `min < max` may not always hold.

---

### Setting Coordinate Systems

Programmers can define custom coordinate systems to control how real-number coordinates map to pixel space.  
A typical function might look like:

```text
setCoordinateSystem(left, right, bottom, top)
````

> [!note]  
> This function defines how the user’s coordinate system corresponds to display coordinates.

---

### Manual Transformation (When No Function Available)

If the graphics library does not provide automatic coordinate mapping, transformation can be computed manually.

Let:

- Old coordinate system: `oldLeft`, `oldRight`, `oldBottom`, `oldTop`
    
- New coordinate system: `newLeft`, `newRight`, `newBottom`, `newTop`
    
- A point `(oldX, oldY)` to transform into `(newX, newY)`
    

Then:

$$  
newX = newLeft + \frac{(oldX - oldLeft)}{(oldRight - oldLeft)} \times (newRight - newLeft)  
$$

$$  
newY = newBottom + \frac{(oldY - oldBottom)}{(oldTop - oldBottom)} \times (newTop - newBottom)  
$$

> [!tip]  
> This formula maintains the relative position of a point between two coordinate systems.

---

### Explanation

> [!info]
> 
> - The term $(oldX - oldLeft)/(oldRight - oldLeft)$ gives the **fractional position** of `oldX` within the old range.
>     
> - Multiplying by $(newRight - newLeft)$ rescales that position to the new coordinate width.
>     
> - Adding `newLeft` aligns the new coordinate origin.
>     
> - The same logic applies for Y-coordinates.
>     

---

### Key Idea

> [!important]  
> The point `(oldX, oldY)` preserves its **relative position** within the rectangular region after transformation.  
> This ensures proportional placement across coordinate systems.

In most modern graphics pipelines, these coordinate transformations are handled automatically, but understanding them is crucial for implementing **custom mapping functions** or **low-level rendering systems**.