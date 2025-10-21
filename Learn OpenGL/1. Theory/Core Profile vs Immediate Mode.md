> [!summary]  
> **Modern OpenGL = Core Profile.** Immediate mode and fixed-function are deprecated. Learn **OpenGL 3.3 Core**: programmable pipeline, shaders, buffers, VAOs. Newer versions add features, not new fundamentals.

---

### From Immediate Mode to Core Profile

- **Legacy (immediate/fixed-function):** `glBegin/glEnd`, built-in transforms, fixed lighting. Easy, **inefficient**, and **deprecated**.
    
- **Modern (core profile ≥ 3.2):** programmable pipeline. You manage **shaders**, **VBO/IBO**, **VAO**, and **uniforms**.
    

> [!warning]  
> Deprecated calls only error in a **core** context. In a **compatibility** context they may still run, but you should avoid them.

---

### Why 3.3 is a solid target

- **Stable baseline:** all core concepts used in production: VAO/VBO, GLSL 330, FBO, textures, depth test, blending.
    
- **Forward knowledge:** 4.x adds **features** (DSA, compute, tessellation, multi-draw, bindless) but **not** new basics.
    
- **Portability:** 3.3 runs on a wide range of hardware and drivers.
    

> [!tip]  
> Learn 3.3 Core thoroughly. Then “opt-in” to 4.x features when your **target hardware** supports them.

---

### Practical implications

- You must provide **shaders**:
    
    ```glsl
    #version 330 core
    layout(location=0) in vec3 aPos;
    uniform mat4 uModel, uView, uProj;
    void main(){ gl_Position = uProj * uView * uModel * vec4(aPos,1.0); }
    ```
    
- You must provide **buffers** and a **VAO**:
    
    ```cpp
    glGenVertexArrays(1,&vao);
    glBindVertexArray(vao);
    glGenBuffers(1,&vbo);
    glBindBuffer(GL_ARRAY_BUFFER, vbo);
    glBufferData(GL_ARRAY_BUFFER, size, data, GL_STATIC_DRAW);
    glVertexAttribPointer(0,3,GL_FLOAT,GL_FALSE, stride, (void*)0);
    glEnableVertexAttribArray(0);
    ```
    
- You control **state** explicitly: depth, blend, cull, viewport, FBOs.
    

---

### Version strategy

- Target **OpenGL 3.3 Core** as default.
    
- Gate newer features by **version/extension checks**:
    
    ```cpp
    int major, minor;
    glGetIntegerv(GL_MAJOR_VERSION,&major);
    glGetIntegerv(GL_MINOR_VERSION,&minor);
    // or check GLAD/extension flags before using 4.x features
    ```
    

> [!info]  
> macOS typically exposes at most **OpenGL 4.1** and is effectively **frozen**. Windows/Linux depend on GPU drivers. Always **update drivers**.

---

### Core vs. Compatibility

- **Core:** only modern API; deprecated entry points removed. Recommended for learning and production.
    
- **Compatibility:** includes legacy calls for old codebases; can mask bad habits.
    

> [!important]  
> If you see `glMatrixMode`, `glTranslatef`, `glBegin/glEnd`, `glLight*`, `glu*`: that’s **legacy**. Replace with **MVP uniforms**, **VBO/VAO**, and **GLSL**.

---

### Extensions and capabilities

- New functionality often ships as **extensions** before it lands in a core version.
    
- Load via your loader (e.g., **GLAD**) and **check** availability before use.
    

---

### Key takeaways

> [!important]
> 
> - Learn **3.3 Core**: the mental model scales to 4.x.
>     
> - Write shaders, manage buffers, set state explicitly.
>     
> - Prefer **core contexts**; avoid fixed-function.
>     
> - Enable higher-version features only when **available** on target hardware.
>