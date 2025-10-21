> [!summary]  
> **OpenGL objects = state containers.** You create a named object, bind it to a target, set parameters or upload data, then draw using the current bindings. Modern 3.3+ core relies on this **generate → bind → configure → use** pattern. Some APIs in 4.5+ allow **DSA** without binding.

---

### What an OpenGL “object” is

- A handle (GLuint id) referencing driver-managed state and resources.
    
- Examples: **buffer**, **vertex array (VAO)**, **texture**, **sampler**, **framebuffer (FBO)**, **renderbuffer**, **shader**, **program**, **query**, **sync**.
    
- Binding associates an object with a **target** to edit or use it (e.g., `GL_ARRAY_BUFFER`, `GL_TEXTURE_2D`, `GL_FRAMEBUFFER`).
    

> [!warning]  
> Names like `GL_WINDOW_TARGET` in pseudo-code are **not real**. Window creation is handled by GLFW/SDL; it is **not** an OpenGL object.

---

### Classic pattern (3.3 core): generate → bind → configure

> [!example] Buffer + VAO
> 
> ```cpp
> // Generate
> GLuint vao=0, vbo=0, ebo=0;
> glGenVertexArrays(1,&vao);
> glGenBuffers(1,&vbo);
> glGenBuffers(1,&ebo);
> 
> // Bind VAO (captures vertex format + element buffer binding)
> glBindVertexArray(vao);
> 
> // Vertex buffer
> glBindBuffer(GL_ARRAY_BUFFER, vbo);
> glBufferData(GL_ARRAY_BUFFER, verticesSize, vertices, GL_STATIC_DRAW);
> 
> // Index buffer
> glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, ebo);
> glBufferData(GL_ELEMENT_ARRAY_BUFFER, indicesSize, indices, GL_STATIC_DRAW);
> 
> // Vertex attribs
> glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, stride, (void*)0);
> glEnableVertexAttribArray(0);
> 
> // Safe unbind (keep EBO bound to the VAO)
> glBindBuffer(GL_ARRAY_BUFFER, 0);
> glBindVertexArray(0);
> ```
> 
> Draw later:
> 
> ```cpp
> glUseProgram(program);
> glBindVertexArray(vao);
> glDrawElements(GL_TRIANGLES, indexCount, GL_UNSIGNED_INT, 0);
> glBindVertexArray(0);
> ```

> [!info]  
> **VAO** captures attribute enables, `glVertexAttribPointer` formats, and the **currently bound** `GL_ELEMENT_ARRAY_BUFFER`. It does **not** store your `GL_ARRAY_BUFFER` binding after setup.

---

### Textures and parameters

> [!example] 2D texture setup
> 
> ```cpp
> GLuint tex=0;
> glGenTextures(1,&tex);
> glBindTexture(GL_TEXTURE_2D, tex);
> glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
> glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
> glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
> glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
> glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA8, w, h, 0, GL_RGBA, GL_UNSIGNED_BYTE, pixels);
> glGenerateMipmap(GL_TEXTURE_2D);
> glBindTexture(GL_TEXTURE_2D, 0);
> ```
> 
> Bind for use:
> 
> ```cpp
> glActiveTexture(GL_TEXTURE0);
> glBindTexture(GL_TEXTURE_2D, tex);
> glUniform1i(uTex0, 0);
> ```

---

### Programs and shaders

> [!example] Minimal link and use
> 
> ```cpp
> GLuint vs = glCreateShader(GL_VERTEX_SHADER);
> glShaderSource(vs, 1, &vsSrc, nullptr); glCompileShader(vs);
> GLuint fs = glCreateShader(GL_FRAGMENT_SHADER);
> glShaderSource(fs, 1, &fsSrc, nullptr); glCompileShader(fs);
> 
> GLuint prog = glCreateProgram();
> glAttachShader(prog, vs); glAttachShader(prog, fs); glLinkProgram(prog);
> glDeleteShader(vs); glDeleteShader(fs); // after link
> 
> glUseProgram(prog);
> ```

---

### Framebuffers

> [!example] Offscreen FBO
> 
> ```cpp
> GLuint fbo=0, color=0, depth=0;
> glGenFramebuffers(1,&fbo);
> glBindFramebuffer(GL_FRAMEBUFFER, fbo);
> 
> glGenTextures(1,&color);
> glBindTexture(GL_TEXTURE_2D, color);
> glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA8, W, H, 0, GL_RGBA, GL_UNSIGNED_BYTE, nullptr);
> glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, color, 0);
> 
> glGenRenderbuffers(1,&depth);
> glBindRenderbuffer(GL_RENDERBUFFER, depth);
> glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, W, H);
> glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, depth);
> 
> assert(glCheckFramebufferStatus(GL_FRAMEBUFFER) == GL_FRAMEBUFFER_COMPLETE);
> glBindFramebuffer(GL_FRAMEBUFFER, 0);
> ```

---

### Object lifetime and defaults

- **Generate:** `glGen*` reserves names; storage is allocated when first bound/defined.
    
- **Delete:** `glDelete*` releases when no longer referenced; binding to 0 reverts to the **default object** for that target (e.g., default framebuffer `0`).
    
- **State sticks:** once set on an object, parameters persist across binds until changed.
    

> [!warning]  
> Unbinding by binding `0` affects the **context**, not the object. Don’t assume “reset”; explicitly set required state in each pass.

---

### Direct State Access (DSA, 4.5+ or ARB extensions)

Avoids temporary binds for setup:

> [!example] With DSA (fast path if available)
> 
> ```cpp
> GLuint vbo=0; glCreateBuffers(1,&vbo);
> glNamedBufferData(vbo, size, data, GL_STATIC_DRAW);  // no bind needed
> 
> GLuint tex=0; glCreateTextures(GL_TEXTURE_2D, 1, &tex);
> glTextureStorage2D(tex, 1, GL_RGBA8, w, h);
> glTextureSubImage2D(tex, 0, 0,0, w,h, GL_RGBA, GL_UNSIGNED_BYTE, pixels);
> glTextureParameteri(tex, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
> ```
> 
> Keep a capability flag to fall back to 3.3 binding path if DSA is unavailable.

---

### Common pitfalls

> [!warning]
> 
> - Forgetting a **VAO** in core profile → draw calls fail.
>     
> - Changing `GL_ELEMENT_ARRAY_BUFFER` **after** VAO setup detaches indices from that VAO.
>     
> - Assuming a VAO stores current `GL_ARRAY_BUFFER` binding beyond attribute setup. It doesn’t.
>     
> - Mutating objects while bound to a different target or FBO → undefined behavior.
>     
> - Silent state leakage between passes. Re-establish required state per pass.
>     

---

### Mental model

> [!important]  
> Treat each OpenGL object as a **state capsule** you configure once and bind when needed. Batch draws by shared object state to minimize binds and maximize throughput.