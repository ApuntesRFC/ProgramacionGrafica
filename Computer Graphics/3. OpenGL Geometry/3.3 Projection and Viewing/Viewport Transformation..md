> [!summary]  
> **Viewport transform** maps **NDC** $x,y\in[-1,1]$ to **window pixels** in a chosen rectangle. Set it with `glViewport(x, y, width, height)`. Update it on framebuffer resize. Keep aspect-consistent projection.

---

### Concept: Viewport Transform (modern OpenGL 3.3+)

- Pipeline reminder: **Object → World → Eye → Clip → divide by $w$ → NDC → Viewport → Window**.
    
- **Viewport** operates **after** the perspective divide. It maps NDC to a rectangular pixel area in the current framebuffer.
    

> [!info]  
> OpenGL window coordinates place $(0,0)$ at the **lower-left** of the drawable by default. Many UI toolkits report window sizes from a top-left origin. Account for this when doing mouse picking.

---

### API

```c
// OpenGL 3.3 Core
void glViewport(GLint x, GLint y, GLsizei width, GLsizei height);
```

- `(x, y)`: lower-left corner of the viewport in **window** coordinates.
    
- `width, height`: size in pixels.
    
- Initially the viewport covers the entire drawable. You can set multiple viewports over time to render split views.
    

---

### Math: NDC → Window

Given viewport origin $(x_0,y_0)$ and size $(w_\text{vp},h_\text{vp})$, and NDC $(x_\text{ndc},y_\text{ndc},z_\text{ndc})$:  
$$  
\begin{aligned}  
x_\text{win}&=\tfrac{x_\text{ndc}+1}{2},w_\text{vp}+x_0\  
y_\text{win}&=\tfrac{y_\text{ndc}+1}{2},h_\text{vp}+y_0\  
z_\text{win}&=\tfrac{z_\text{ndc}+1}{2}\in[0,1]  
\end{aligned}  
$$

> [!note]  
> Clipping is done **in clip space** before the divide by $w$. The viewport does **not** clip geometry; it only maps coordinates. To clip rasterization to a rectangle, use the **scissor test**.

---

### Multiple views on one drawable

Side-by-side example on a 600×400 framebuffer:

```c
// Left view: 300x400 at (0,0)
glViewport(0, 0, 300, 400);
// draw first scene

// Right view: 300x400 at (300,0)
glViewport(300, 0, 300, 400);
// draw second scene
```

> [!tip]  
> When using multiple viewports, also adjust your **projection** per view to the correct aspect ratio.

---

### Resize handling

- OpenGL does **not** auto-update the viewport when the window/framebuffer size changes.
    
- In GLFW, update in the framebuffer-size callback:
    

```c
void framebuffer_size_callback(GLFWwindow*, int w, int h){
    glViewport(0, 0, w, h);
}
```

> [!important]  
> On resize, recompute your projection:  
> $$ P=\text{perspective}(\text{fov},\ \text{aspect}=\tfrac{w}{h},\ z_\text{near},\ z_\text{far}) $$

---

### Common pitfalls (corrected)

> [!warning]  
> “Viewport maps **clip** coordinates.”  
> **Correction:** It maps **NDC** (after divide by $w$) to window coordinates.

> [!warning]  
> “Viewport limits drawing.”  
> **Nuance:** It defines the mapping region. To **limit** rasterization to a rectangle, enable scissor:
> 
> ```c
> glEnable(GL_SCISSOR_TEST);
> glScissor(x0, y0, w, h);
> ```

> [!warning]  
> “If the viewport extends outside the drawable it errors.”  
> **No.** It is allowed. Rendering is naturally constrained by the framebuffer bounds.

---

### Quick checklist

- Set `glViewport` once at init and on **every** resize.
    
- Keep your projection’s aspect in sync with the viewport.
    
- For split screens, set a viewport per pass and draw.
    
- Use **scissor** for hard clip rectangles.