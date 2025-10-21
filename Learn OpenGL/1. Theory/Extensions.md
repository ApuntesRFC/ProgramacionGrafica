> [!summary]  
> **OpenGL extensions = feature switches.** Vendors ship new capabilities via extensions before they land in core. You must **query** availability at runtime, then enable a **fast path** or fall back. Target **3.3 Core** first; use extensions opportunistically.

---

### What extensions are

- Vendor- or ARB-defined additions that expose new features or optimizations.
    
- Prefixes: `GL_ARB_*` (cross-vendor), `GL_KHR_*` (Khronos), `GL_EXT_*` (add-on), vendor-specific (`GL_NV_*`, `GL_AMD_*`, `GL_INTEL_*`).
    
- Popular ARB extensions are often absorbed into later core versions.
    

> [!info]  
> The spec guarantees behavior, not implementation. If an extension is reported, you can rely on its documented behavior.

---

### Querying support (modern 3.3+)

> [!example] Enumerate and test extensions
> 
> ```cpp
> // Query version
> int major=0, minor=0;
> glGetIntegerv(GL_MAJOR_VERSION, &major);
> glGetIntegerv(GL_MINOR_VERSION, &minor);
> 
> // Count extensions
> GLint nExt = 0;
> glGetIntegerv(GL_NUM_EXTENSIONS, &nExt);
> 
> auto hasExt = [&](const char* name)->bool{
>     for(int i=0;i<nExt;++i){
>         const char* ext = reinterpret_cast<const char*>(glGetStringi(GL_EXTENSIONS, i));
>         if(std::strcmp(ext, name)==0) return true;
>     }
>     return false;
> };
> 
> bool hasDSA   = hasExt("GL_ARB_direct_state_access");   // example
> bool hasDebug = hasExt("GL_KHR_debug");                  // or GL_ARB_debug_output on older stacks
> ```
> 
> Use your loader’s helpers when available (e.g., GLAD exposes feature macros/flags).

> [!warning]  
> `glGetString(GL_EXTENSIONS)` is **deprecated** beyond 3.0 core; use `glGetStringi`.

---

### Version vs. extension strategy

- Prefer **core version checks** when a feature is in core:
    
    - Example: **debug output** is core in **4.3** (`GL_KHR_debug`), else fall back to the extension if present.
        
- Keep a small **feature table** at startup and branch your code:
    
    ```cpp
    struct Caps { bool debug; bool dsa; bool texStorage; } caps;
    caps.debug = (major>4 || (major==4 && minor>=3)) || hasExt("GL_KHR_debug");
    caps.dsa   = (major>4 || (major==4 && minor>=5)) || hasExt("GL_ARB_direct_state_access");
    caps.texStorage = (major>4 || (major==4 && minor>=2)) || hasExt("GL_ARB_texture_storage");
    ```
    

> [!tip]  
> Ship **one codepath** per feature: fast path if `caps.feature` else a portable fallback.

---

### Typical high-impact extensions

- **GL_KHR_debug / GL_ARB_debug_output**: structured debug messages and labels.
    
- **GL_ARB_direct_state_access (DSA)**: bindless-like object mutation without rebinding.
    
- **GL_ARB_texture_storage**: immutable textures, fewer errors, better driver hints.
    
- **GL_ARB_multi_draw_indirect / GL_ARB_draw_indirect**: CPU submission reduction.
    
- **GL_ARB_timer_query**: precise GPU timing.
    

> [!note]  
> Names may differ across vendors or be promoted to core with minor API changes—guard carefully.

---

### Robustness and fallbacks

> [!example] Pattern
> 
> ```cpp
> if (caps.dsa) {
>     glCreateBuffers(1,&vbo);
>     glNamedBufferData(vbo, size, data, GL_STATIC_DRAW);
> } else {
>     glGenBuffers(1,&vbo);
>     glBindBuffer(GL_ARRAY_BUFFER, vbo);
>     glBufferData(GL_ARRAY_BUFFER, size, data, GL_STATIC_DRAW);
>     glBindBuffer(GL_ARRAY_BUFFER, 0);
> }
> ```
> 
> Same idea for debug labels, texture creation, and multi-draw submission.

---

### Testing matrix

- Test on at least:
    
    - One **NVIDIA**, one **AMD**, one **Intel** driver.
        
    - **Windows** and **Linux** (macOS OpenGL is frozen; prefer Metal there).
        
- Log:
    
    ```cpp
    const GLubyte* renderer = glGetString(GL_RENDERER);
    const GLubyte* vendor   = glGetString(GL_VENDOR);
    const GLubyte* version  = glGetString(GL_VERSION);
    ```
    

---

### Key takeaways

> [!important]
> 
> - Target **OpenGL 3.3 Core** for portability.
>     
> - **Query** extensions at runtime; never assume availability.
>     
> - Maintain a **capability table** and branch to **fast paths** when supported.
>     
> - Prefer **core features** when present; use extensions as incremental upgrades.
>