> [!summary]  
> **Mouse input** allows us to look around by updating **yaw** and **pitch** values.  
> We capture the mouse cursor and calculate offsets to control camera rotation.

---

### 1. Capturing the Cursor

First, tell GLFW to **hide and capture** the cursor:

```cpp
glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);
```

Once the application has focus, the mouse cursor:

- Stays **within the center** of the window
    
- Is **invisible**
    
- Won't leave the window
    

Perfect for an FPS camera system!

---

### 2. Mouse Callback Function

Create a callback function to handle mouse movement:

```cpp
void mouse_callback(GLFWwindow* window, double xpos, double ypos);
```

Register it with GLFW:

```cpp
glfwSetCursorPosCallback(window, mouse_callback);
```

This function is called **every time the mouse moves**.

---

### 3. Steps for Camera Direction

1. **Calculate** the mouse's offset since the last frame
    
2. **Add** the offset values to yaw and pitch
    
3. **Add constraints** to min/max pitch values
    
4. **Calculate** the direction vector
    

---

### 4. Implementation

#### Initialize Last Mouse Position:

```cpp
float lastX = 400.0f, lastY = 300.0f;
bool firstMouse = true;
```

#### Mouse Callback Function:

```cpp
void mouse_callback(GLFWwindow* window, double xpos, double ypos)
{
    if (firstMouse)
    {
        lastX = (float)xpos;
        lastY = (float)ypos;
        firstMouse = false;
    }

    float xoffset = (float)xpos - lastX;
    float yoffset = lastY - (float)ypos; // reversed: y ranges bottom to top
    lastX = (float)xpos;
    lastY = (float)ypos;

    const float sensitivity = 0.1f;
    xoffset *= sensitivity;
    yoffset *= sensitivity;

    yaw   += xoffset;
    pitch += yoffset;

    // Constrain pitch
    if (pitch > 89.0f)  pitch = 89.0f;
    if (pitch < -89.0f) pitch = -89.0f;

    // Calculate direction vector
    glm::vec3 direction;
    direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
    direction.y = sin(glm::radians(pitch));
    direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
    cameraFront = glm::normalize(direction);
}
```

---

### 5. Key Details

#### Sensitivity:

Multiply offsets by a **sensitivity value** to control mouse responsiveness.

#### Pitch Constraints:

- **Maximum**: 89 (at 90 the LookAt flips)
    
- **Minimum**: -89
    

This prevents weird camera behavior when looking straight up or down.

#### First Mouse Movement:

The `firstMouse` boolean prevents a **large jump** when the cursor first enters the window.

---

> [!info]
> 
> - The computed direction vector contains all **rotations from mouse movement**.
>     
> - Since `cameraFront` is used in `glm::lookAt()`, we're set to go!
>     
> - No constraint on **yaw** allows for full **horizontal rotation**.
>     
> - You can easily add yaw constraints if needed.
