> [!summary]  
> **Zooming** is implemented by adjusting the **field of view (FOV)** in the projection matrix.  
> We use the **mouse scroll wheel** to control zoom level.

---

### 1. Concept

The **Field of View** (FOV) defines how much of the scene we can see:

- **Smaller FOV**  narrower view  **zoom in** effect
    
- **Larger FOV**  wider view  **zoom out** effect
    

When FOV becomes smaller, the scene's projected space gets smaller, but is still projected over the same NDC, giving the **illusion of zooming in**.

---

### 2. Scroll Callback

Similar to mouse movement and keyboard input, we have a callback function for mouse scrolling:

```cpp
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset)
{
    Zoom -= (float)yoffset;
    
    if (Zoom < 1.0f)  Zoom = 1.0f;
    if (Zoom > 45.0f) Zoom = 45.0f;
}
```

#### Parameters:

- **yoffset**: Amount scrolled vertically
    

#### Constraints:

- **Minimum**: 1.0 (very zoomed in)
    
- **Maximum**: 45.0 (default FOV)
    

---

### 3. Register the Callback

```cpp
glfwSetScrollCallback(window, scroll_callback);
```

---

### 4. Use FOV in Projection Matrix

Each frame, upload the projection matrix with the updated `fov` value:

```cpp
projection = glm::perspective(
    glm::radians(fov), 
    800.0f / 600.0f, 
    0.1f, 
    100.0f
);
```

---

### 5. Result

You now have a **simple camera system** that allows for:

- **Free movement** in 3D environment (WASD)
    
- **Looking around** with mouse
    
- **Zooming** with scroll wheel
    

---

> [!info]
> 
> - Since **45.0** is the default FOV value, we constrain zoom between **1.0 and 45.0**.
>     
> - Feel free to experiment with different ranges!
>     
> - Source code reference: `/src/1.getting_started/7.3.camera_mouse_zoom/`
