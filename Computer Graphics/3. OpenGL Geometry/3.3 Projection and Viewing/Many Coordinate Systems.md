
> [!summary]  
> **Object → World → Eye → Clip → NDC → Window.** Apply **Model**, then **View**, then **Projection**. Do **clipping in clip space**, divide by **(w)** to get **NDC**, then the **Viewport** maps to pixels. Keep (z) for depth testing.

---

### Coordinate spaces and intent

- **Object space:** local coordinates for authoring the mesh.
    
- **World space:** shared scene frame where objects are placed.
    
- **Eye/View space:** camera at (0,0,0) looking along (-Z), with (+Y) up and (+X) right.
    
- **Clip space:** output of (P); clipping happens **before** dividing by (w).
    
- **NDC:** normalized device coordinates in (-1,1) after dividing by (w).
    
- **Window/Device space:** pixel coordinates inside the viewport rectangle.
    

> [!warning]  
> Legacy OpenGL combined **Model** and **View** as **modelview**. Modern OpenGL (core profile) requires you to build and pass the matrices.

---

### Matrices and one-line formula

Let **(M)** = Model, **(V)** = View, **(P)** = Projection. With $(\mathbf{p}_{obj}=(x,y,z,1)^\top)$:  
$$ 
\mathbf{p}_{clip}=P,V,M,\mathbf{p}_{obj},\qquad  
\mathbf{p}_{ndc}=\frac{\mathbf{p}_{clip}}{w_{clip}}$$

---

### Step-by-step pipeline

1. **Model transform** ((M))  
    Place the object in the world.  
    $(\mathbf{p}_{world}=M,\mathbf{p}_{obj})$
    
2. **View transform** ((V))  
    Move world into the camera frame (inverse of camera pose).  
    $(\mathbf{p}_{eye}=V,\mathbf{p}_{world})$
    
3. **Projection transform** ((P))  
    Map the visible region (frustum or box) to clip space.  
    $(\mathbf{p}_{clip}=P,\mathbf{p}_{eye})$
    
4. **Clipping in clip space**  
    Before dividing by (w):  
    $(-w \le x \le w,\quad -w \le y \le w,\quad -w \le z \le w.)$
    
5. **Perspective divide → NDC**  
    $((x,y,z,w)\mapsto(x/w,\ y/w,\ z/w))$.
    In OpenGL, near plane maps to $(z_{ndc}=-1)$ and far plane to $(+1)$.
    
6. **Viewport transform → window/device**  
    For viewport origin $((x_0,y_0))$ and size $((w_{vp},h_{vp}))$:  
    $$\begin{aligned}  
    x_{win}&=\tfrac{x_{ndc}+1}{2}\cdot w_{vp}+x_0\  
    y_{win}&=\tfrac{y_{ndc}+1}{2}\cdot h_{vp}+y_0\  
    z_{win}&=\tfrac{z_{ndc}+1}{2}\in[0,1]  
    \end{aligned}  
    $$ 
    Rasterization then produces fragments that are depth-tested.
    

---

### Projection choices

- **Perspective:** foreshortening; view volume is a frustum.
    
- **Orthographic:** parallel projection; view volume is an axis-aligned box.
    

> [!tip]  
> Use tight near/far planes. A smaller (f/n) ratio improves depth precision and reduces z-fighting.

---

### Depth buffer facts

- Depth comparisons use $(z_{win}\in[0,1])$ by default.
    
- With perspective, precision is non-linear and highest near the camera.
    

---

### Common misconceptions (corrected)

> [!warning]  
> “We project to 2D by discarding (z).”  
> **Incorrect.** (z) survives as $(z_{ndc})$ then maps to $(z_{win}\in[0,1])$ for depth testing.

> [!warning]  
> “If $(P=I)$ and $(V=M=I)$, clip coordinates already match the canonical cube.”  
> **Partial.** You are in **clip space** with (w=1). You only see geometry that **already lies** in $(-1,1)$ on all axes; there is no mapping.

> [!warning]  
> “OpenGL uses world coordinates internally.”  
> **No.** You compose (P,V,M) and pass it; OpenGL works with clip/NDC/window spaces.

---

### Minimal modern usage (GLM + GLSL)

> [!example]  
> **C++ (GLM)**
> 
> ```cpp
> glm::mat4 M = glm::mat4(1.0f);
> M = glm::translate(M, {tx, ty, tz});
> M = glm::rotate(M, glm::radians(angleDeg), glm::normalize(glm::vec3(ax, ay, az)));
> M = glm::scale(M, {sx, sy, sz});
> 
> glm::mat4 V = glm::lookAt(camPos, camPos + camDir, camUp);
> glm::mat4 P = glm::perspective(glm::radians(fovDeg), aspect, zNear, zFar);
> 
> glUseProgram(program);
> glUniformMatrix4fv(glGetUniformLocation(program,"uModel"), 1, GL_FALSE, glm::value_ptr(M));
> glUniformMatrix4fv(glGetUniformLocation(program,"uView"),  1, GL_FALSE, glm::value_ptr(V));
> glUniformMatrix4fv(glGetUniformLocation(program,"uProj"),  1, GL_FALSE, glm::value_ptr(P));
> ```
> 
> **GLSL (vertex shader)**
> 
> ```glsl
> #version 330 core
> layout(location=0) in vec3 aPos;
> uniform mat4 uModel, uView, uProj;
> void main() {
>     gl_Position = uProj * uView * uModel * vec4(aPos, 1.0);
> }
> ```

---

> [!important]  
> Build (M,V,P) on the CPU → pass as uniforms → clip in clip space → divide by (w) → NDC → viewport to pixels. Keep (z) for depth.