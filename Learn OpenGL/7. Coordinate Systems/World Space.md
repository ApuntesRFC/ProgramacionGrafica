> [!summary]  
> **World space** defines where an object is placed, scaled, and oriented within the larger 3D world.  
> Vertices are transformed from **local space** to **world space** using the **model matrix**.

---

### 1. Concept

- After modeling, every object begins at its own origin (local space).
    
- To place it in the scene, we apply the **model matrix**, which combines:
    
    - **Translation** → moves the object to a world position.
        
    - **Rotation** → orients the object correctly.
        
    - **Scaling** → adjusts its size.
        

This transformation converts local coordinates into coordinates relative to the **global world origin (0, 0, 0)**.

---

### 2. Example

A house model in local space might be centered at (0, 0, 0).  
Using the model matrix, we can move and orient it in the world:

```cpp
glm::mat4 model = glm::mat4(1.0f);
model = glm::translate(model, glm::vec3(5.0f, 0.0f, -10.0f)); // move house
model = glm::rotate(model, glm::radians(20.0f), glm::vec3(0.0f, 1.0f, 0.0f)); // face road
model = glm::scale(model, glm::vec3(0.8f)); // resize
```

Each vertex of the house (in local space) is multiplied by this matrix:  
$$  
\text{WorldCoords} = \text{ModelMatrix} \cdot \text{LocalCoords}  
$$

---

> [!info]
> 
> - The **model matrix** allows multiple instances of the same mesh to exist at different positions/orientations without modifying the original data.
>     
> - World space is shared by all objects — physics, lighting, and collisions are typically calculated here.
>     
> - The resulting coordinates will later be transformed into **view space** using the **view matrix**:
>     
>     ```glsl
>     gl_Position = projection * view * model * vec4(aPos, 1.0);
>     ```
>