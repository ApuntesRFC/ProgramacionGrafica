> [!summary]  
> Rendering in OpenGL happens continuously inside a **render loop**.  
> The loop runs until the user closes the window, handling input events and refreshing the screen using a **double-buffering system**.

---

### Step — Create the Render Loop

```cpp
while (!glfwWindowShouldClose(window)) {
    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

---

### Explanation

|Function|Purpose|
|---|---|
|`glfwWindowShouldClose(window)`|Returns `true` when the user requests to close the window (e.g., presses the close button).|
|`glfwSwapBuffers(window)`|Swaps the **back** and **front** buffers to display the rendered frame.|
|`glfwPollEvents()`|Processes pending events such as keyboard, mouse, or window resizing, and calls registered callbacks.|

> [!note]  
> The loop keeps the window active and responsive. Without it, the application would close instantly after one frame.

---

### Concept — Double Buffering

To avoid flickering and partially drawn frames, **modern rendering** uses two buffers:

|Buffer|Description|
|---|---|
|**Back buffer**|The buffer where the frame is currently being rendered.|
|**Front buffer**|The buffer currently visible on the screen.|

When rendering finishes:

```cpp
glfwSwapBuffers(window);
```

swaps the **back buffer → front buffer**, displaying the complete image instantly.

---

### Visualization of the Process

> [!info]
> 
> 1. Draw the next frame → Back buffer
>     
> 2. Swap buffers → Back becomes Front
>     
> 3. Display the complete frame on screen
>     
> 4. Repeat for each iteration of the render loop
>     

This ensures **smooth, artifact-free output** since the user never sees a partially drawn image.

---

### Simplified Execution Flow

> [!important]
> 
> 1. Initialize GLFW and create the window.
>     
> 2. Initialize GLAD.
>     
> 3. Set the viewport and callbacks.
>     
> 4. **Enter the render loop** (`while(!glfwWindowShouldClose(window))`).
>     
> 5. Inside the loop:
>     
>     - Handle input with `glfwPollEvents()`.
>         
>     - Swap buffers with `glfwSwapBuffers(window)`.
>         
> 6. When the user closes the window, exit the loop and call `glfwTerminate()`.
>     

---

### Full Minimal Example

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height);
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
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
        std::cout << "Failed to initialize GLAD" << std::endl;
        return -1;
    }

    glViewport(0, 0, 800, 600);
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // Render loop
    while (!glfwWindowShouldClose(window)) {
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

> [!tip]
> 
> - The render loop will later include actual **drawing commands**.
>     
> - For now, it just keeps the window open and responsive while managing buffer swapping and event polling.

