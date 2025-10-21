
> [!summary]
3D transforms extend 2D: **translation**, **scaling**, and **rotation**. In modern OpenGL, build a **model matrix** on the CPU (e.g., GLM) and pass it as a **uniform** to the vertex shader. Do not use fixed-function calls like `glTranslatef/glRotatef/glScalef`.

---

### 3D Transforms: Core Definitions

- **Translation:** $(x,y,z)\mapsto(x+d_x,\;y+d_y,\;z+d_z)$  
- **Scaling:** $(x,y,z)\mapsto(s_x x,\;s_y y,\;s_z z)$  
- **Rotation about axis** $\mathbf{u}=(a_x,a_y,a_z)$ by angle $r$ (right-hand rule).  
  Positive $r$ rotates from $+x$ toward $+y$ around $+z$ in a right-handed system.

> [!note]
> Reflections use **negative scales** (e.g., $s_z=-1$ mirrors through the $xy$-plane) and flip winding. Adjust face culling if needed.

---

### Modern OpenGL Usage (GLM + uniforms)

**C++ (GLM) — build Model matrix**
```cpp
glm::mat4 model = glm::mat4(1.0f);

// translation
model = glm::translate(model, glm::vec3(dx, dy, dz));

// rotation around arbitrary axis (degrees → radians)
model = glm::rotate(model, glm::radians(angleDeg), glm::normalize(glm::vec3(ax, ay, az)));

// scaling (uniform or non-uniform)
model = glm::scale(model, glm::vec3(sx, sy, sz));

// send to shader
glUseProgram(program);
glUniformMatrix4fv(glGetUniformLocation(program,"uModel"), 1, GL_FALSE, glm::value_ptr(model));
````

**GLSL (vertex shader) — apply MVP**

```glsl
#version 330 core
layout(location=0) in vec3 aPos;
uniform mat4 uModel, uView, uProj;
void main() {
    gl_Position = uProj * uView * uModel * vec4(aPos, 1.0);
}
```

> [!warning]  
> Fixed-function calls `glTranslatef`, `glRotatef`, `glScalef` are **deprecated** and not available in core profile. Always compose matrices yourself.

---

### Order of Operations

Typical local-to-world composition: **Scale → Rotate → Translate**.  
In code with GLM you multiply onto `model` in that semantic order; the resulting matrix applies them correctly when used as `uModel`.

> [!tip]  
> To rotate around a point $p=(p_x,p_y,p_z)$:  
> `model = T(p) * R * T(-p) * model`.

---

### 2D with OpenGL (in the $xy$-plane)

Draw with $z=0$ and use 3D transforms constrained to 2D:

- Translate: `glm::translate(model, {dx, dy, 0})`
    
- Rotate about $z$: `glm::rotate(model, r, {0,0,1})`
    
- Scale: `glm::scale(model, {sx, sy, 1})`
    

**Orthographic projection** is convenient:

```cpp
glm::mat4 proj = glm::ortho(left, right, bottom, top, zNear, zFar);
```

---

### Quick Reference: 4×4 Matrices

**Translation**  
$$  
T(d_x,d_y,d_z)=  
\begin{bmatrix}  
1&0&0&d_x\  
0&1&0&d_y\  
0&0&1&d_z\  
0&0&0&1  
\end{bmatrix}  
$$

**Scaling**  
$$  
S(s_x,s_y,s_z)=  
\begin{bmatrix}  
s_x&0&0&0\  
0&s_y&0&0\  
0&0&s_z&0\  
0&0&0&1  
\end{bmatrix}  
$$

**Rotation about $z$** (analogous for $x$/$y$)  
$$  
R_z(r)=  
\begin{bmatrix}  
\cos r&-\sin r&0&0\  
\sin r& \cos r&0&0\  
0&0&1&0\  
0&0&0&1  
\end{bmatrix}  
$$

---

### Examples

> [!example] Translation
> 
> ```cpp
> model = glm::mat4(1.0f);
> model = glm::translate(model, { 2.0f, 0.5f, -1.0f });
> ```

> [!example] Rotation about arbitrary axis
> 
> ```cpp
> model = glm::mat4(1.0f);
> model = glm::rotate(model, glm::radians(45.0f), glm::normalize(glm::vec3(1,1,0)));
> ```

> [!example] Reflection through the $xy$-plane
> 
> ```cpp
> model = glm::mat4(1.0f);
> model = glm::scale(model, { 1.0f, 1.0f, -1.0f }); // flips Z
> glFrontFace(GL_CW); // if needed to compensate winding after mirror
> ```

---

### Handedness and angles

- Use **right-hand rule** for positive angles in right-handed spaces.
    
- GLM expects **radians**; wrap degrees with `glm::radians()`.
    
- Mirroring (negative scale) flips handedness and can invert normals; recompute or adjust normal matrix for lighting.
    

> [!important]  
> The key pattern: **build transforms on CPU → send matrices → apply in vertex shader**. This is the modern, portable way to use transforms in OpenGL.