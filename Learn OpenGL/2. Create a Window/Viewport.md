> [!summary]  
> Before rendering, you must define the **OpenGL viewport**, which maps **Normalized Device Coordinates (−1 → 1)** to actual **window pixels**.  
> You also register a **resize callback** to keep the viewport consistent when the window is resized.

---

### Step — Define the Viewport

```cpp
glViewport(0, 0, 800, 600);
```

---

### Explanation

- `glViewport(x, y, width, height)` defines the rectangular area of the window that OpenGL draws into.
    
- Parameters:
    
| Parameter         | Meaning                                      |
| ----------------- | -------------------------------------------- |
| `x`, `y`          | Lower-left corner of the viewport in pixels. |
| `width`, `height` | Dimensions of the rendering area in pixels.  |

    

> [!info]  
> Usually, you match the viewport to your window size,  
> but you can make it **smaller** if you want to render OpenGL output in only part of the window.

---

### Internal Mapping

OpenGL’s **clip-space coordinates** range from **−1 to +1** in each axis.  
The viewport transforms this range to pixel coordinates.

$$  
(-1, 1) \rightarrow (0, 800) \text{ on X-axis,} \quad  
(-1, 1) \rightarrow (0, 600) \text{ on Y-axis.}  
$$

> [!note]  
> For example, a vertex at $(-0.5, 0.5)$ maps roughly to pixel coordinates $(200, 450)$ in an 800×600 window.

---

### Step — Handle Window Resizing

When the user resizes the window, the viewport must adjust to the new framebuffer size.

```cpp
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height);
}
```

---

### Step — Register the Callback

After creating the window and before the render loop, register your callback:

```cpp
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

> [!important]  
> This ensures that every time the window changes size,  
> **GLFW automatically calls** your callback with the new width and height.  
> The first time the window appears, the callback is also called once with the current dimensions.

---

### Why This Matters

Without updating the viewport, the rendered scene would stretch, distort, or clip when resizing the window.  
This callback ensures OpenGL’s projection always matches the actual framebuffer size.

> [!tip]  
> On **Retina displays (macOS)**, the framebuffer size reported in this callback is **larger** than the logical window size,  
> because macOS uses high-DPI scaling. Always use the provided `width` and `height` directly.

---

### Full Example Context

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

    return 0;
}
```

> [!important]  
> **Call `glViewport()` once after GLAD initialization** and **register the callback** to handle resizing.  
> This ensures OpenGL always renders correctly within the visible window region.