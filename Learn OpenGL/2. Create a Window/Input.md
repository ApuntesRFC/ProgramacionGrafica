> [!summary]  
> GLFW provides input handling functions like **`glfwGetKey`**, allowing you to check whether a specific key is pressed or released.  
> You’ll use this to close the window when **Esc** is pressed.

---

### Step — Create an Input Function

```cpp
void processInput(GLFWwindow* window) {
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}
```

---

### Explanation

|Function|Purpose|
|---|---|
|`glfwGetKey(window, key)`|Returns the state of the given key — either `GLFW_PRESS` or `GLFW_RELEASE`.|
|`glfwSetWindowShouldClose(window, true)`|Flags the window to close; causes `glfwWindowShouldClose(window)` to return `true` in the next loop iteration.|

> [!info]  
> Here, if the **Escape key** is pressed, `processInput` sets the window’s close flag to `true`.  
> The next time the render loop checks `glfwWindowShouldClose`, it exits.

---

### Step — Integrate Into the Render Loop

```cpp
while (!glfwWindowShouldClose(window)) {
    processInput(window);       // Handle user input
    glfwSwapBuffers(window);    // Swap color buffers
    glfwPollEvents();           // Process events
}
```

> [!note]  
> Each loop iteration corresponds to **one frame**.  
> Calling `processInput()` every frame ensures input is responsive and consistent.

---

### Concept — Frame and Input Updates

> [!info]  
> A **frame** = one complete update + render cycle.  
> Inside each frame:
> 
> 1. Check input (`processInput`).
>     
> 2. Update state or animations (optional).
>     
> 3. Render the scene.
>     
> 4. Swap buffers and poll events.
>     

---

### Full Example Context

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height);
}

void processInput(GLFWwindow* window) {
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", nullptr, nullptr);
    if (!window) {
        std::cout << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
        std::cout << "Failed to initialize GLAD" << std::endl;
        return -1;
    }

    // Main render loop
    while (!glfwWindowShouldClose(window)) {
        processInput(window);
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

> [!tip]  
> `processInput()` is a good place to handle all future keyboard or mouse inputs — movement, camera control, etc.  
> Keep it separate from rendering logic for cleaner code organization.
