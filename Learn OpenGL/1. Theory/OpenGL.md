> [!summary]  
> **OpenGL is a specification, not a library.** Khronos defines results; GPU vendors implement them in drivers. Your code calls an API (headers + loader) that forwards to the driver. Use **Core 3.3+**, keep drivers updated.

---

### What OpenGL is (and is not)

- **Specification (Khronos):** defines function behavior and outputs, not implementations.
    
- **Implementations (drivers):** NVIDIA/AMD/Intel/Apple/Linux stacks provide the actual code.
    
- **Your API surface:** headers + function loader (e.g., **GLAD**) + context/windowing (e.g., **GLFW**).
    

> [!info]  
> Different vendors may implement internals differently. They must still match the spec’s observable results.

---

### Versions and support

- A GPU + driver pair exposes a **max OpenGL version**.
    
- On Windows, update **GPU drivers** to get fixes and newer versions.
    
- On macOS, OpenGL is **system-provided and frozen** at older versions; prefer Metal there for modern work.
    
- On Linux, implementations come from Mesa or vendor drivers.
    

---

### Practical workflow (modern)

1. Create a context with **GLFW** (or SDL).
    
2. Load function pointers with **GLAD** for **OpenGL 3.3 Core**.
    
3. Query capabilities if needed:
    
    ```c
    int major, minor; 
    glGetIntegerv(GL_MAJOR_VERSION,&major); 
    glGetIntegerv(GL_MINOR_VERSION,&minor);
    ```
    
4. Develop with the **Core Profile** (no fixed-function, no matrix stacks).
    

---

### Debugging and stability

- Bugs that look “OpenGL-weird” often are **driver** issues → update drivers.
    
- Enable the **debug output** to catch API misuse:
    
    ```c
    glEnable(GL_DEBUG_OUTPUT);
    glDebugMessageCallback(callback, nullptr);
    ```
    

---

### Specs and references

- Read the **OpenGL 3.3 Core Specification** for authoritative behavior.
    
- Remember: the spec describes **what must happen**, not **how** vendors implement it.
    

> [!important]  
> Think in layers: **App code → OpenGL API (GLAD/headers) → Driver implementation → GPU**.