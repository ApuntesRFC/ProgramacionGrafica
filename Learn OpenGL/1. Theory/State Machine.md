> [!summary]  
> **OpenGL is a state machine.** You mutate **context state** with state-changing calls, then state-using calls act on that state to produce output. Manage and minimize state changes for predictable, fast rendering.

---

### Core idea

- **OpenGL context = state container.** It stores current bindings, enables, parameters, and programs.
    
- Two kinds of calls:
    
    - **State-changing:** set or bind something (`glUseProgram`, `glBind*`, `glEnable`, `glDisable`, `glViewport`, `glBlendFunc`, sampler params, uniforms).
        
    - **State-using:** consume current state (`glDraw*`, `glDispatchCompute`, `glClear`).
        

> [!info]  
> In modern OpenGL (3.3+ core) you **must** bind a **VAO** before drawing. VAO captures vertex input state; VBOs hold data; the **program** provides shaders.

---

### Typical state groups you control

- **Program/shaders:** `glUseProgram(program)`.
    
- **Vertex input:** `glBindVertexArray(vao)`, `glBindBuffer(GL_ARRAY_BUFFER,vbo)`, `glVertexAttribPointer`, `glEnableVertexAttribArray`.
    
- **Textures & samplers:** `glActiveTexture`, `glBindTexture`, `glTexParameter*` or separate `GL_SAMPLER`.
    
- **Framebuffers:** `glBindFramebuffer(GL_FRAMEBUFFER, fbo)`.
    
- **Fixed tests:** `glEnable(GL_DEPTH_TEST)`, `glDepthFunc`, `glEnable(GL_CULL_FACE)`, `glCullFace`, `glFrontFace`, `glEnable(GL_BLEND)`, `glBlendFunc/Equation`, `glEnable(GL_SCISSOR_TEST)`.
    
- **Viewport & clipping:** `glViewport`, `glScissor`.
    
- **Uniforms & UBOs:** `glUniform*`, `glBindBufferBase(GL_UNIFORM_BUFFER, ...)`.
    

---

### Minimal draw sequence (3.3 core)

```cpp
// State-changing setup
glViewport(0,0,width,height);
glUseProgram(program);
glBindVertexArray(vao);
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, tex);
glUniformMatrix4fv(uProj,1,GL_FALSE,glm::value_ptr(P));
glUniformMatrix4fv(uView,1,GL_FALSE,glm::value_ptr(V));
glUniformMatrix4fv(uModel,1,GL_FALSE,glm::value_ptr(M));
glEnable(GL_DEPTH_TEST);

// State-using
glDrawElements(GL_TRIANGLES, indexCount, GL_UNSIGNED_INT, 0);
```

---

### Performance and correctness

> [!tip]  
> **Minimize state churn.** Batch draw calls by material/program, then by textures, then by VAO. Fewer `glUseProgram`, `glBindTexture`, and blend/depth changes → higher throughput.

> [!tip]  
> **Initialize state explicitly.** Do not rely on defaults. Set depth test, culling, blend, and viewport on startup and after any context loss.

> [!warning]  
> **State leaks.** A change persists until you undo it. Encapsulate render passes and restore or re-establish required state before each pass.

---

### Inspecting and debugging state

- Query version/caps:
    
    ```cpp
	    int maj, min; 
	    glGetIntegerv(GL_MAJOR_VERSION,&maj);
	    glGetIntegerv(GL_MINOR_VERSION,&min);
    ```
    
- Query enables or bindings:
    
    ```cpp
    GLboolean depth = glIsEnabled(GL_DEPTH_TEST);
    GLint prog=0; 
    glGetIntegerv(GL_CURRENT_PROGRAM,&prog);
    ```
    
- Debug output:
    
    ```cpp
    glEnable(GL_DEBUG_OUTPUT);
    glDebugMessageCallback(callback,nullptr);
    ```
    

---

### Multiple contexts and threading

- A context is **thread-affine**; make it current in exactly one thread at a time.
    
- Contexts can **share objects** (textures, buffers, programs) if created with sharing enabled.
    
- Never mutate a GL object from two contexts simultaneously without explicit sync.
    

---

### State design patterns (3.3+ portable)

- **VAO-per-mesh** to capture vertex format.
    
- **UBOs** for per-frame/per-material constants to reduce uniform calls.
    
- **State setters** that early-out if the value already matches cached state on the CPU.
    
- **Render passes** that set a known baseline (depth, blend, cull, FBO, viewport) at the start.
    

---

### Key takeaways

> [!important]
> 
> - Think: **set state → draw**.
>     
> - Batch to reduce **state changes**.
>     
> - Make state explicit and restore as needed.
>     
> - Use VAOs, UBOs, and debug output to tame the state machine.
>