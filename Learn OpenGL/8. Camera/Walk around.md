> [!summary]  
> Swinging the camera around is fun, but it's more engaging to control movement ourselves using **keyboard input**.  
> We'll implement **WASD** controls to walk forward, backward, and strafe left/right.

---

### 1. Camera System Setup

First, define camera variables at the top of the program:

```cpp
glm::vec3 cameraPos   = glm::vec3(0.0f, 0.0f, 3.0f);
glm::vec3 cameraFront = glm::vec3(0.0f, 0.0f, -1.0f);
glm::vec3 cameraUp    = glm::vec3(0.0f, 1.0f, 0.0f);
```

---

### 2. Update LookAt Function

The LookAt function now becomes:

```cpp
view = glm::lookAt(cameraPos, cameraPos + cameraFront, cameraUp);
```

- **Position**: `cameraPos` (where the camera is)
    
- **Target**: `cameraPos + cameraFront` (where the camera looks)
    
- **Up**: `cameraUp` (camera's up direction)
    

This ensures that however we move, the camera keeps looking at the **target direction**.

---

### 3. Keyboard Input for Movement

Let's add keyboard controls in the `processInput` function:

```cpp
void processInput(GLFWwindow *window)
{
    const float cameraSpeed = 0.05f; // adjust accordingly
    
    if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
        cameraPos += cameraSpeed * cameraFront;
    if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
        cameraPos -= cameraSpeed * cameraFront;
    if (glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
        cameraPos -= glm::normalize(glm::cross(cameraFront, cameraUp)) * cameraSpeed;
    if (glfwGetKey(window, GLFW_KEY_D) == GLFW_PRESS)
        cameraPos += glm::normalize(glm::cross(cameraFront, cameraUp)) * cameraSpeed;
}
```

---

### 4. Movement Explanation

#### Forward/Backward (W/S):

- **W**: Add the `cameraFront` vector scaled by speed
    
- **S**: Subtract the `cameraFront` vector
    

#### Strafe Left/Right (A/D):

- Do a **cross product** to create a **right vector**
    
- **Normalize** it to ensure consistent movement speed
    
- Move along the right vector accordingly
    

This creates the familiar **strafe effect** when using the camera.

---

> [!note]
> 
> - We **normalize** the resulting right vector to ensure **consistent movement speed** regardless of the camera's orientation.
>     
> - Without normalization, the cross product may return differently sized vectors based on `cameraFront`.
>     
> - The movement speed is currently **system-specific**  we'll fix this in the next section with **delta time**.
