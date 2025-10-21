> [!summary]  
> **Modeling vs Viewing = same math, different intent.** In modern OpenGL you build **$M$** (per-object) and a single **$V$** (per-frame) on the CPU. The viewer-moving idea is the **inverse** of moving the world. Manage matrices yourself; no fixed-function stacks.

---

### Concept: Modeling–Viewing equivalence

- Any rigid transform can be seen either as moving the **object** (modeling) or moving the **camera** (viewing).
    
- The camera transform applied to vertices is the **inverse** of the camera pose.
    
- Practically: many objects share one **view** transform $V$, while each object has its own **model** transform $M$.
    

> [!info]  
> Vertex path: $$\mathbf{p}_\text{clip}=P,V,M,\mathbf{p}_\text{obj},\qquad \mathbf{p}_\text{ndc}=\frac{\mathbf{p}_\text{clip}}{w}.$$

---

### Inverses and order

- Moving the viewer left by $5$ equals moving the world right by $5$.
    
- Rotating the camera by $-90^\circ$ about $y$ equals rotating all objects by $+90^\circ$ about $y$.
    
- **Order matters:** modeling transforms compose right-to-left on the vector; viewing applies the **inverse** order of the camera pose.
    

> [!example] Object vs. camera
> 
> - Modeling: $$M = T(10,0,0),R_y(90^\circ)$$ first **rotate**, then **translate** in effect.
>     
> - Viewing: camera pose $C = R_y(+90^\circ),T(-10,0,0)$, and the applied matrix is $$V=C^{-1}=T(10,0,0),R_y(-90^\circ).$$  
>     Both produce the **same** image.
>     

---

### Modern OpenGL (3.3+ core) usage

Do **not** use `glMatrixMode/glLoadIdentity/glTranslatef/glRotatef/glScalef/gluLookAt`. Build matrices on CPU and pass as uniforms.

**C++ (GLM)**

```cpp
// Camera (view)
glm::mat4 V = glm::lookAt(
    camPos,                // eye
    camPos + camDir,       // center
    camUp                  // up
);

// Per-object model
glm::mat4 M = glm::mat4(1.0f);
M = glm::translate(M, {tx, ty, tz});
M = glm::rotate(M, glm::radians(angleDeg), glm::normalize(glm::vec3(ax, ay, az)));
M = glm::scale(M, {sx, sy, sz});

// Projection (perspective example)
glm::mat4 P = glm::perspective(glm::radians(fovyDeg), aspect, zNear, zFar);

// Upload
glUseProgram(program);
glUniformMatrix4fv(glGetUniformLocation(program,"uView"), 1, GL_FALSE, glm::value_ptr(V));
glUniformMatrix4fv(glGetUniformLocation(program,"uProj"), 1, GL_FALSE, glm::value_ptr(P));
glUniformMatrix4fv(glGetUniformLocation(program,"uModel"),1, GL_FALSE, glm::value_ptr(M));
```

**GLSL (vertex)**

```glsl
#version 330 core
layout(location=0) in vec3 aPos;
uniform mat4 uModel, uView, uProj;
void main() {
    gl_Position = uProj * uView * uModel * vec4(aPos, 1.0);
}
```

---

### Practical drawing sequence

1. Compute **$V$** once per frame from camera state.
    
2. For each object: build **$M$**, bind pipeline, set uniforms, draw.
    
3. Keep **$P$** updated when the viewport size changes to preserve aspect.
    

> [!tip]  
> Treat the camera as a pose $C$ in world space. Build $$V=C^{-1}$$ explicitly. This clarifies why “moving the camera” is equivalent to moving the world oppositely.

---

### Correcting legacy statements

> [!warning]  
> “Use `glMatrixMode(GL_MODELVIEW)`, `glPushMatrix/glPopMatrix`, and `gluLookAt`.”  
> **Modern fix:** No fixed-function stacks in core profile. Use GLM (or your math) to compose $M$, $V$, $P$ and pass them as uniforms.

> [!warning]  
> “Scaling the viewer equals world expansion, so use viewer scaling.”  
> **Clarification:** The **mathematical** effect on the image is equivalent, but in practice you scale the **world** in $M$ or adjust **FOV** in $P$; avoid non-uniform scaling in $V$ which can break assumptions for culling or lighting.

---

### Minimal camera cookbook

- Position: $$\text{camPos} \in \mathbb{R}^3.$$
    
- Orientation: build basis $(\mathbf{r},\mathbf{u},\mathbf{f})$ from yaw/pitch/roll or a quaternion.
    
- View: $$V=\text{lookAt}(\text{camPos},,\text{camPos}+\mathbf{f},,\mathbf{u}).$$
    
- Move camera → update $\text{camPos}$ or orientation → recompute $V$.
    
- Equivalent “move world” view is implicit via applying $V$ to all vertices.
    

---

### See it interactively

The legacy demo illustrates modeling–viewing equivalence conceptually:  
[https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c3/transform-equivalence-3d.html](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c3/transform-equivalence-3d.html)

> [!important]  
> Mental model: **$V$ moves the world into the camera**, **$M$ places the object into the world**. Same math, different semantics.