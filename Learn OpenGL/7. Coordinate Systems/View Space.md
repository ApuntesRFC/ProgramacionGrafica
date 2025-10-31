> [!summary]  
> **View space** (also called **camera space** or **eye space**) represents the scene **from the camera’s point of view**.  
> It is obtained by transforming **world coordinates** with the **view matrix**.

---

### 1. Concept

- In **world space**, objects are positioned relative to the global origin.
    
- To render the scene from the **camera’s perspective**, we must move everything so the **camera** becomes the new origin.
    
- This transformation is done via the **view matrix**, which applies:
    
    - A **translation** to move the world opposite to the camera’s position.
        
    - A **rotation** to align the world with the camera’s orientation.
        

---

### 2. Formula

$$  
\text{ViewCoords} = \text{ViewMatrix} \cdot \text{WorldCoords}  
$$

In code (using GLM):

```cpp
glm::mat4 view = glm::mat4(1.0f);
view = glm::translate(view, glm::vec3(0.0f, 0.0f, -3.0f)); // move camera backwards
```

> This doesn’t actually move the camera — it moves the **world** in the opposite direction,  
> giving the illusion that the camera moved forward.

---

### 3. Practical Example

When you look around in a 3D game:

- You are **not moving the camera object** directly.
    
- Instead, the **entire scene is transformed inversely** by your camera’s position and orientation.
    

---

> [!info]
> 
> - The **view matrix** acts like the camera’s transformation inverse — it tells OpenGL **how to transform the world so the camera stays still**.
>     
> - Commonly generated with `glm::lookAt(position, target, up)` in GLM.
>     
> - The result is passed to the vertex shader along with the **model** and **projection** matrices:
>     
>     ```glsl
>     gl_Position = projection * view * model * vec4(aPos, 1.0);
>     ```
>