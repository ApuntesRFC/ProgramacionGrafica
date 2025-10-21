> [!summary]  
> **Projection = Eye → Clip.** It defines the **view volume** and maps it to the canonical cube, then the **divide by $w$** gives NDC. In modern OpenGL 3.3+ you build the projection matrix on the CPU (e.g., GLM) and pass it as a uniform. Do **not** use `glMatrixMode`, `glFrustum`, `glOrtho`, or GLU.

---

### What projection does

- A 3D image shows only a subset of the world. That subset is the **view volume**.
    
- **View transform** sets camera pose. **Projection** sets the **shape and extent** of what is visible.
    
- Mathematically:  
    $$  
    \mathbf{p}_\text{clip}=P,\mathbf{p}_\text{eye},\quad  
    \text{then};\mathbf{p}_\text{ndc}=\frac{\mathbf{p}_\text{clip}}{w_\text{clip}},;;x,y,z\in[-1,1].  
    $$
    
- Clipping happens in **clip space** against the canonical cube before the divide.
    

> [!note]  
> OpenGL maps the **near plane** to $z_\text{ndc}=-1$ and the **far plane** to $z_\text{ndc}=+1$.

---

### Types of projection

#### Perspective projection

- Physically plausible. Farther objects appear smaller. Only geometry **in front of the camera** is visible.
    
- Idealized infinite pyramid becomes a **frustum** once you select finite near/far planes.
    

Parameters:

- Vertical field of view $fovy$ (degrees).
    
- Aspect ratio $a=\tfrac{\text{width}}{\text{height}}$.
    
- Positive distances $z_\text{near}>0$, $z_\text{far}>z_\text{near}$.
    

**GLM usage**

```cpp
glm::mat4 P = glm::perspective(glm::radians(fovyDeg), aspect, zNear, zFar);
```

**Depth precision tip**

> [!tip]  
> Keep $z_\text{near}$ as **large** as acceptable and $z_\text{far}$ as **small** as possible. A smaller $z_\text{far}/z_\text{near}$ ratio reduces z-fighting.

#### Orthographic projection

- Parallel projection. No foreshortening. Good for CAD/UI or true-size inspection.
    
- View volume is a rectangular box. Objects can be in front **or behind** the eye as long as they lie between near and far.
    

Parameters:

- Box extents: $(l,r,b,t)$ at all depths.
    
- Near and far: $z_\text{near}$ and $z_\text{far}$ with $z_\text{far}>z_\text{near}$; $z_\text{near}$ may be **negative**.
    

**GLM usage**

```cpp
glm::mat4 P = glm::ortho(left, right, bottom, top, zNear, zFar);
```

---

### Modern pipeline integration

**CPU side**

```cpp
glm::mat4 V = glm::lookAt(camPos, camPos + camDir, camUp);
glm::mat4 P = glm::perspective(glm::radians(fovyDeg), aspect, zNear, zFar);

glUseProgram(program);
glUniformMatrix4fv(glGetUniformLocation(program,"uView"), 1, GL_FALSE, glm::value_ptr(V));
glUniformMatrix4fv(glGetUniformLocation(program,"uProj"), 1, GL_FALSE, glm::value_ptr(P));
```

**Vertex shader**

```glsl
#version 330 core
layout(location=0) in vec3 aPos;
uniform mat4 uModel, uView, uProj;
void main() {
    gl_Position = uProj * uView * uModel * vec4(aPos, 1.0);
}
```

> [!important]  
> `glMatrixMode`, `glLoadIdentity`, `glFrustum`, `glOrtho`, and `gluPerspective` are **deprecated/removed** in core profile. Manage matrices yourself and send them as uniforms.

---

### View volume, clipping, and $z$

- The six **clipping planes** (left, right, bottom, top, near, far) bound the frustum or box.
    
- Projection maps those planes to the faces of the canonical cube in clip space; then clipping occurs.
    
- After perspective divide, depth goes to window space:  
    $$  
    z_\text{win}=\frac{z_\text{ndc}+1}{2}\in[0,1].  
    $$
    

> [!info]  
> The viewport transform then maps $(x_\text{ndc},y_\text{ndc})$ to pixels. Use the **scissor test** if you must hard-clip rasterization to a rectangle.

---

### Correcting legacy statements

> [!warning]  
> “Use `glMatrixMode(GL_PROJECTION)` and `glFrustum/glOrtho`.”  
> **Modern fix:** Build $P$ with GLM and set it via a uniform. No matrix stacks in core profile.

> [!warning]  
> “Orthographic = discard $z$.”  
> **Correction:** Orthographic keeps $z$ for depth testing and ordering; it removes foreshortening, not depth.

> [!warning]  
> “Projection flips $z$ by scaling with $-1$.”  
> **Clarification:** The handedness change is a result of the canonical NDC mapping. Do **not** manually add a $-1$ scale; use the standard projection matrices.

---

### Practical checklist

- Pick **projection type** that fits the task (perspective for scenes, ortho for UI/CAD).
    
- On window resize: update **viewport** and recompute **aspect** in $P$.
    
- Keep near/far **tight** for better depth precision.
    
- Pass $M$, $V$, $P$ as uniforms; never rely on fixed-function matrix state.