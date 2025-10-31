> [!summary]  
> This section initializes **GLFW** and sets up a minimal **OpenGL 3.3 Core Profile** context.  
> You will:
> 
> 1. Include `glad` and `glfw3`.
>     
> 2. Initialize GLFW.
>     
> 3. Specify the desired OpenGL version/profile.
>     
> 4. Create a window and set its context.
>     

---

### Step 1 — Include GLAD and GLFW

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```

> [!important]  
> Always include **GLAD before GLFW**.  
> GLAD includes the necessary OpenGL headers (e.g., `GL/gl.h`), so including GLFW first would cause redefinition or missing symbol errors.

---

### Step 2 — Basic `main()` Structure

```cpp
int main() {
    glfwInit();  // Initialize the GLFW library
    
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);  // Request OpenGL major version 3
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);  // Request OpenGL minor version 3
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);  // Request Core Profile
    
    // For macOS compatibility
    // glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
    
    return 0;
}
```

---

### Explanation

- `glfwInit()` initializes the GLFW library. Must be called **before any other GLFW function**.
    
- `glfwWindowHint(option, value)` sets configuration **hints** before creating a window:
    
    - `GLFW_CONTEXT_VERSION_MAJOR`: OpenGL major version.
        
    - `GLFW_CONTEXT_VERSION_MINOR`: OpenGL minor version.
        
    - `GLFW_OPENGL_PROFILE`: selects **Core** or **Compatibility** profile.
        

> [!info]
> 
> - Use **Core Profile** (`GLFW_OPENGL_CORE_PROFILE`) for modern OpenGL (no deprecated features).
>     
> - On macOS, add
>     
>     ```cpp
>     glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
>     ```
>     
>     This is required for Core Profile contexts.
>     

---

### Step 3 — Verify OpenGL Version Support

Ensure your hardware and drivers support **OpenGL ≥ 3.3**.  
Otherwise, context creation will fail or behave unpredictably.

> [!tip]
> 
> - **Windows:** Use _OpenGL Extensions Viewer_.
>     
> - **Linux:** Run `glxinfo | grep "OpenGL version"`.
>     
> - **macOS:** Limited to OpenGL 4.1; no updates beyond that.
>     

---

### Step 4 — Create the Window

```cpp
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", nullptr, nullptr);
if (window == nullptr) {
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);  // Set current OpenGL context
```

---

### Explanation

|Function|Purpose|
|---|---|
|`glfwCreateWindow(width, height, title, monitor, share)`|Creates a window and context. Returns a pointer to a `GLFWwindow`.|
|`glfwMakeContextCurrent(window)`|Activates the created window’s context on the current thread.|
|`glfwTerminate()`|Cleans up GLFW resources if initialization fails.|

> [!note]  
> The last two parameters of `glfwCreateWindow` are typically `nullptr` for single-window setups.
> 
> - `monitor`: use for fullscreen mode.
>     
> - `share`: share resources between contexts.
>     

---

### Step 5 — Summary of Initialization Flow

> [!important]
> 
> 1. Initialize GLFW (`glfwInit()`).
>     
> 2. Configure version and profile (`glfwWindowHint()`).
>     
> 3. Create a window (`glfwCreateWindow()`).
>     
> 4. Make its context current (`glfwMakeContextCurrent()`).
>     
> 5. Later, initialize GLAD to load OpenGL function pointers (next step).
>     

---

### Minimal Complete Example

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    // glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE); // for macOS

    GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", nullptr, nullptr);
    if (window == nullptr) {
        std::cout << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    // Placeholder for GLAD initialization (next section)
    // gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);

    return 0;
}
```

> [!tip]  
> If you see **“undefined reference”** errors during compilation, it means **GLFW was not linked correctly**.  
> Make sure your CMake or linker includes the GLFW library properly.