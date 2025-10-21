> [!summary]  
> To render with OpenGL you need: a **window + OpenGL context** (via a windowing library) and a **function loader** (to call modern `gl*`). Target **OpenGL 3.3 Core**; avoid legacy APIs.

---

### Context, Windowing, and Input

- **OpenGL** does not create windows or handle input.
    
- A **context** encapsulates OpenGL state and the version/profile.
    
- Use a windowing library to:
    
    - create a window and **OpenGL context**,
        
    - process **input events**,
        
    - **swap buffers** and run the main loop.
        
- Common choices: **GLFW**, **SDL**, **SFML**, **GLUT** (legacy).
    

> [!info]  
> Request a **Core Profile** context to ensure the programmable pipeline and to block deprecated calls.

---

### What GLFW Provides

- Cross-platform window creation and **OpenGL context** setup.
    
- **Input** handling (keyboard, mouse, gamepads).
    
- **Timing** and monitor/vidmode queries.
    
- Buffer swapping and window events.
    

> [!note]  
> GLFW is C-based and minimal by design. It does not load OpenGL functions; pair it with a loader like **GLAD**.

---

### Function Loading with GLAD

- OpenGL function pointers are resolved **at runtime** by the driver.
    
- **GLAD** generates headers and loader code for a chosen version/profile and extensions.
    
- After initializing GLAD you can call modern `gl*` APIs safely.
    

> [!warning]  
> Manual `wglGetProcAddress/glXGetProcAddress` is OS-specific and error-prone. Prefer a loader.

---

### Build System: CMake (Concept)

- **CMake** generates platform/IDE build files from a single config.
    
- Keeps your OpenGL project **portable** across Windows/Linux/macOS.
    
- Plays well with GLFW, GLAD, GLM, stb_image, etc.
    

---

### Minimal Runtime Flow

1. Create window + **OpenGL context** with a windowing lib (e.g., GLFW).
    
2. Initialize **GLAD** (function pointers).
    
3. Query caps if needed:
    
    ```cpp
    const char* vendor   = (const char*)glGetString(GL_VENDOR);
    const char* renderer = (const char*)glGetString(GL_RENDERER);
    const char* version  = (const char*)glGetString(GL_VERSION);
    ```
    
4. Set viewport, states, and enter the render loop.
    

---

### Targets and Profiles

- Baseline: **OpenGL 3.3 Core** (GLSL 330, VAO/VBO, FBOs, textures).
    
- Newer versions add features but **not** new fundamentals; opt in when available.
    

> [!important]  
> Think in layers: **App code → Windowing (GLFW) → Loader (GLAD) → OpenGL driver → GPU**.