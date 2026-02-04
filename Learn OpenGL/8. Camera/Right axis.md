> [!summary]  
> The **right vector** represents the positive $x$-axis of the camera space.  
> We compute it using the **cross product** of the world up vector and the camera direction.

---

### Computing the Right Vector

We use a simple trick:

1. Start with an **up vector** that points upwards in world space.
    
2. Take the **cross product** of the up vector and the direction vector.
    

Since the **cross product** produces a vector **perpendicular** to both input vectors, we get a vector pointing in the **positive $x$-axis** direction of the camera:

```cpp
glm::vec3 up = glm::vec3(0.0f, 1.0f, 0.0f);
glm::vec3 cameraRight = glm::normalize(glm::cross(up, cameraDirection));
```

---

> [!info]
> 
> - If we **switched the cross product order**, we'd get a vector pointing in the **negative $x$-axis** direction.
>     
> - The cross product order matters: $\vec{a} \times \vec{b} = -\vec{b} \times \vec{a}$.
>     
> - This right vector forms the first axis of our camera coordinate system.