> [!summary]  
> Before calling any `gl*` functions, **GLAD must be initialized**.  
> GLAD loads the correct OpenGL function pointers from the driver through the **windowing library (GLFW)**.

---

### Step — Initialize GLAD

```cpp
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
    std::cout << "Failed to initialize GLAD" << std::endl;
    return -1;
}
```

---

### Explanation

- `gladLoadGLLoader()` loads **all OpenGL function pointers** dynamically at runtime.
    
- It requires a **platform-specific function** to query addresses. GLFW provides this through:
    
    ```cpp
    glfwGetProcAddress
    ```
    
- The cast `(GLADloadproc)` ensures the correct function signature for GLAD.
    

> [!important]  
> **GLAD must be initialized _after_ creating the OpenGL context** (i.e., after `glfwMakeContextCurrent(window)`),  
> or it won’t be able to fetch the OpenGL function addresses from the driver.

---

### Why This Step Is Necessary

OpenGL functions like `glGenBuffers` or `glDrawArrays` are **not linked** at compile time.  
They are **resolved at runtime** because OpenGL drivers load their implementations dynamically.

> [!info]  
> GLAD queries these implementations and fills in the correct **function pointers**, allowing you to use the API normally.

---

### Simplified Initialization Flow

> [!important]
> 
> 1. Initialize **GLFW**.
>     
> 2. Create window and context.
>     
> 3. Make context current.
>     
> 4. Initialize **GLAD** with `gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)`.
>     
> 5. Now it is safe to call OpenGL functions.
>     

---

### Minimal Code Context

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

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

    return 0;
}
```

> [!tip]  
> If you see “**Failed to initialize GLAD**,” it usually means:
> 
> - The context wasn’t made current before calling `gladLoadGLLoader`.
>     
> - The GLFW or GLAD libraries weren’t linked or generated correctly.
>