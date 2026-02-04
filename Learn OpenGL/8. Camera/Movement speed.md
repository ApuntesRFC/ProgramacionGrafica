> [!summary]  
> Movement speed should be **frame-rate independent** to ensure consistent experience across different hardware.  
> We use **delta time** to normalize movement speed.

---

### 1. The Problem

Currently, we use a **constant value** for movement speed. This has issues:

- **Faster machines** render more frames per second  call `processInput` more often  **move faster**
    
- **Slower machines** render fewer frames  call `processInput` less often  **move slower**
    

This creates an **inconsistent experience** depending on hardware.

---

### 2. The Solution: Delta Time

Graphics applications track a **deltaTime** variable that stores the time it took to render the last frame.

We then **multiply all velocities** by this `deltaTime` value:

- When `deltaTime` is **large** (frame took longer)  velocity is **higher** to balance out
    
- When `deltaTime` is **small** (frame was fast)  velocity is **lower**
    

---

### 3. Implementation

#### Track Delta Time:

```cpp
float deltaTime = 0.0f; // Time between current frame and last frame
float lastFrame = 0.0f; // Time of last frame
```

#### Calculate Delta Time Each Frame:

```cpp
float currentFrame = glfwGetTime();
deltaTime = currentFrame - lastFrame;
lastFrame = currentFrame;
```

#### Use Delta Time in Movement:

```cpp
void processInput(GLFWwindow *window)
{
    float cameraSpeed = 2.5f * deltaTime;
    
    if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
        cameraPos += cameraSpeed * cameraFront;
    if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
        cameraPos -= cameraSpeed * cameraFront;
    // ... etc
}
```

---

### 4. Result

Since we're using `deltaTime`, the camera now moves at a **constant speed** of **2.5 units per second**, regardless of frame rate.

---

> [!info]
> 
> - With delta time, the camera **walks and looks equally fast** on any system.
>     
> - The `deltaTime` value will frequently return with **anything movement-related** in graphics programming.
>     
> - Source code reference: `/src/1.getting_started/7.2.camera_keyboard_dt/`
