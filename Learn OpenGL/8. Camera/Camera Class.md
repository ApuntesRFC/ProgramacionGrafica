> [!summary]  
> A **camera class** encapsulates all camera logic into a reusable object.  
> This keeps rendering code clean and makes the camera easy to use across multiple projects.

---

### 1. Why a Camera Class?

In upcoming chapters, we'll always use a camera to look around scenes. Since camera code can take up significant space, we'll **abstract it into a class** that:

- Handles **position**, **orientation**, and **vectors**
    
- Manages **keyboard** and **mouse** input
    
- Provides **view matrix** generation
    
- Can be **reused** across projects
    

---

### 2. Design Overview

Unlike the **Shader** chapter, we won't walk through creating the camera class step-by-step. Instead, **fully commented source code** is provided for you to study.

#### Typical Camera Class Contains:

- **Position**, **Front**, **Up**, **Right**, and **WorldUp** vectors
    
- **Yaw**, **Pitch**, and **Zoom** values
    
- **Movement speed** and **mouse sensitivity**
    
- Methods for:
    
    - `GetViewMatrix()`  returns the view matrix
        
    - `ProcessKeyboard()`  handles WASD movement
        
    - `ProcessMouseMovement()`  updates yaw/pitch
        
    - `ProcessMouseScroll()`  adjusts zoom
        

---

### 3. Implementation Notes

The camera class is typically defined **entirely in a single header file** for convenience.

#### Key Methods:

```cpp
class Camera
{
public:
    glm::vec3 Position;
    glm::vec3 Front;
    glm::vec3 Up;
    glm::vec3 Right;
    glm::vec3 WorldUp;
    
    float Yaw;
    float Pitch;
    float Zoom;
    
    // Returns the view matrix
    glm::mat4 GetViewMatrix()
    {
        return glm::lookAt(Position, Position + Front, Up);
    }
    
    // Processes keyboard input
    void ProcessKeyboard(Camera_Movement direction, float deltaTime);
    
    // Processes mouse movement
    void ProcessMouseMovement(float xoffset, float yoffset);
    
    // Processes mouse scroll
    void ProcessMouseScroll(float yoffset);
};
```

---

### 4. Important Considerations

#### Fly Camera vs FPS Camera:

- The camera system introduced is a **fly-style camera**
    
- It works well with **Euler angles**
    
- Be careful when creating different camera systems (FPS, flight simulation)
    

#### Limitations:

- This fly camera **doesn't allow pitch  90**
    
- A static **up vector** `(0, 1, 0)` doesn't work when **roll** is involved
    
- Each camera type has its **own tricks and quirks**
    

---

### 5. Usage

Once you have the camera class:

```cpp
Camera camera(glm::vec3(0.0f, 0.0f, 3.0f));

// In render loop:
camera.ProcessKeyboard(FORWARD, deltaTime);
camera.ProcessMouseMovement(xoffset, yoffset);

glm::mat4 view = camera.GetViewMatrix();
```

---

> [!info]
> 
> - Source code location: `/includes/learnopengl/camera.h`
>     
> - The code is **fully commented**  study it to understand the implementation.
>     
> - It's advised to **check the class at least once** as an example of creating your own camera system.
>     
> - Updated source code using the camera class: `/src/1.getting_started/7.4.camera_class/`
