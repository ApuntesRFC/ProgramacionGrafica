> [!summary]  
> **Goal:** render many identical meshes by updating only the **model** matrix per draw.  
> Reuse one VAO/VBO. Loop draws. Each iteration sets a new `model`.

---

### Setup: positions array

```cpp
// 10 world-space positions
glm::vec3 cubePositions[] = {
  { 0.0f,  0.0f,  -0.0f},
  { 2.0f,  5.0f, -15.0f},
  {-1.5f, -2.2f,  -2.5f},
  {-3.8f, -2.0f, -12.3f},
  { 2.4f, -0.4f,  -3.5f},
  {-1.7f,  3.0f,  -7.5f},
  { 1.3f, -2.0f,  -2.5f},
  { 1.5f,  2.0f,  -2.5f},
  { 1.5f,  0.2f,  -1.5f},
  {-1.3f,  1.0f,  -1.5f}
};
```

---

### Render loop: per-object model update

```cpp
glBindVertexArray(VAO);

for (unsigned int i = 0; i < 10; ++i) {
    glm::mat4 model = glm::mat4(1.0f);
    model = glm::translate(model, cubePositions[i]);

    float angle = 20.0f * i; // unique rotation per cube
    model = glm::rotate(model,
                        glm::radians(angle),
                        glm::vec3(1.0f, 0.3f, 0.5f));

    ourShader.setMat4("model", model);        // uniform mat4
    glDrawArrays(GL_TRIANGLES, 0, 36);        // cube = 36 verts
}
```

Vertex shader contract:

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec2 aUV;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

out vec2 vUV;

void main() {
    gl_Position = projection * view * model * vec4(aPos, 1.0);
    vUV = aUV;
}
```

---

### Pipeline recap

$$  
\text{clip} = P \cdot V \cdot M_i \cdot  
\begin{pmatrix}\mathbf{aPos}\\1\end{pmatrix}  
\quad\Rightarrow\quad  
\text{NDC} = \frac{\text{clip}}{w}  
$$

- One **VAO/VBO** for the cube geometry.
    
- Change **only** `model` per instance.
    
- Keep `view` and `projection` stable across the loop (update when camera or window changes).
    

---

### Required state

```cpp
glEnable(GL_DEPTH_TEST);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

---

> [!info]
> 
> - **Performance:** Per-draw uniform updates scale linearly. For dozens → fine. For thousands → use **instancing** with per-instance matrices (VBO divisor) or a **uniform/SSBO** with indices.
>     
> - **Rotation center:** Rotations occur around the object origin. To rotate around another pivot: translate to pivot, rotate, translate back.
>     
> - **Order:** Scale → Rotate → Translate when composing `model` to avoid scaling the translation.
>     
> - **State changes:** Avoid rebinding VAO or textures inside the loop unless they differ. Keep shader bound.
>     
> - **Core profile:** Ensure your cube VBO packs 36 vertices with position/UV and that attribute pointers use a stride of `5*sizeof(float)` and offsets `0` and `3*sizeof(float)`.
>