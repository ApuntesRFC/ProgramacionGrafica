> [!summary]  
> **Local space** (also called **object space**) is the coordinate system that belongs to the object itself.  
> All vertex positions are defined relative to the object’s own origin — typically at **(0, 0, 0)**.

---

### 1. Concept

- Every model starts in **its own reference frame**.
    
- The origin (0, 0, 0) is the object’s **pivot or center**.
    
- Transformations such as rotation or scaling occur around this local origin.
    

Example:  
A cube created in Blender has its local origin at (0, 0, 0), even if you later move it to (5, 2, −3) in your scene.

---

### 2. Local Coordinates Example

```text
Vertex positions (cube):
(-0.5, -0.5, -0.5)
( 0.5, -0.5, -0.5)
( 0.5,  0.5, -0.5)
(-0.5,  0.5, -0.5)
...
```

- The cube’s local coordinates range from **−0.5 to +0.5**.
    
- The cube’s local origin is at the **center of the model**.
    
- These coordinates are independent of where the cube appears in the world.
    

---

> [!info]
> 
> - Local space defines the **shape** of the object, not its position.
>     
> - Transforming to **world space** via the **model matrix** places it correctly in the global scene.
>     
> - In shaders, vertex positions usually start in **local space** before being multiplied by `model`, `view`, and `projection` matrices:
>     
>     ```glsl
>     gl_Position = projection * view * model * vec4(aPos, 1.0);
>     ```
>