> [!summary]  
> The **up vector** represents the positive $y$-axis of the camera space.  
> We compute it using the **cross product** of the direction and right vectors.

---

### Computing the Up Vector

Now that we have both the **$x$-axis** (right) and **$z$-axis** (direction) vectors, retrieving the vector that points to the camera's **positive $y$-axis** is straightforward:

```cpp
glm::vec3 cameraUp = glm::cross(cameraDirection, cameraRight);
```

---

### Complete Camera Basis

With the help of the **cross product** and a few tricks, we've successfully created all the vectors that form the **view/camera space**:

- **Right** → camera's $+x$ axis
    
- **Up** → camera's $+y$ axis
    
- **Direction** → camera's $+z$ axis
    
- **Position** → camera's origin
    

---

> [!info]
> 
> - For the mathematically inclined, this process is known as the **Gram-Schmidt process** in linear algebra.
>     
> - Using these camera vectors, we can now create a **LookAt matrix** that proves very useful for camera control.
>     
> - These basis vectors complete the orthonormal coordinate system needed for the view matrix.