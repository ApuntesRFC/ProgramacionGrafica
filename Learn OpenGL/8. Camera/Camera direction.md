> [!summary]  
> The **camera direction** vector indicates which direction the camera is pointing.  
> It's computed by subtracting the target position from the camera position.

---

### Computing the Direction Vector

For now, let the camera point at the **origin** of our scene: $(0, 0, 0)$.

Recall that **subtracting two vectors** gives a vector that is the **difference** between them.

For the view matrix's coordinate system, we want its **$z$-axis to be positive**. Since by convention in OpenGL the camera points towards the **negative $z$-axis**, we negate the direction vector by switching the subtraction order:

```cpp
glm::vec3 cameraTarget = glm::vec3(0.0f, 0.0f, 0.0f);
glm::vec3 cameraDirection = glm::normalize(cameraPos - cameraTarget);
```

This gives us a vector pointing towards the camera's **positive $z$-axis**.

---

> [!note]
> 
> - The name **"direction vector"** is somewhat misleading — it actually points in the **reverse direction** of what the camera is targeting.
>     
> - This vector represents the **positive $z$-axis** of the camera space.