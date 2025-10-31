> [!summary]  
> Build a **textured rotating cube** and fix visibility with **depth testing**.  
> Key steps: set up 36 vertices, VAO/VBO with stride `5*sizeof(float)`, rotate in **model**, enable **GL_DEPTH_TEST**, and clear depth each frame.

---

### Cube geometry and attributes

- A cube = **6 faces × 2 triangles × 3 verts = 36 vertices**.
    
- Pack per-vertex data: **position (x,y,z)** + **texcoord (u,v)** ⇒ **5 floats/vertex**.
    

```cpp
float vertices[] = {
    // pos (x,y,z)        // uv
    -0.5f,-0.5f,-0.5f,    0.0f,0.0f,
     0.5f,-0.5f,-0.5f,    1.0f,0.0f,
     0.5f, 0.5f,-0.5f,    1.0f,1.0f,
     0.5f, 0.5f,-0.5f,    1.0f,1.0f,
    -0.5f, 0.5f,-0.5f,    0.0f,1.0f,
    -0.5f,-0.5f,-0.5f,    0.0f,0.0f,
    // ... (fill remaining faces to total 36 verts)
};
```

**VAO/VBO layout**

```cpp
glBindVertexArray(VAO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// location=0 → vec3 position
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 5*sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// location=1 → vec2 uv
glVertexAttribPointer(1, 2, GL_FLOAT, GL_FALSE, 5*sizeof(float), (void*)(3*sizeof(float)));
glEnableVertexAttribArray(1);
```

---

### Model/View/Projection

**Rotation over time**

```cpp
glm::mat4 model(1.0f);
model = glm::rotate(model,
                    (float)glfwGetTime() * glm::radians(50.0f),
                    glm::vec3(0.5f, 1.0f, 0.0f));
```

**View and Projection**

```cpp
glm::mat4 view(1.0f);
view = glm::translate(view, glm::vec3(0.0f, 0.0f, -3.0f));

glm::mat4 projection =
    glm::perspective(glm::radians(45.0f), width/(float)height, 0.1f, 100.0f);
```

**Vertex shader (core)**

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec2 aTex;

out vec2 TexCoord;

uniform mat4 model, view, projection;

void main() {
    TexCoord = aTex;
    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

---

### Depth testing (z-buffer)

Without depth testing, later fragments overwrite earlier ones arbitrarily. Enable and clear depth.

```cpp
// once at init
glEnable(GL_DEPTH_TEST);                   // default func = GL_LESS

// each frame
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

**Draw**

```cpp
shader.use();
upload(model, view, projection);           // glUniformMatrix4fv...
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 36);
```

---


> [!info]
> 
> - **Near/Far**: choose `near` not too small (e.g., `0.1`) to keep depth precision; very tiny `near` harms precision and causes z-fighting.
>     
> - **Depth func**: keep `GL_LESS`. Use `GL_LEQUAL` only when drawing skyboxes at depth=1.
>     
> - **Face culling** (optional performance/artefact reduction):
>     
>     ```cpp
>     glEnable(GL_CULL_FACE);
>     glCullFace(GL_BACK);
>     glFrontFace(GL_CCW); // match your vertex winding
>     ```
>     
> - **Indexing alternative**: you can reduce duplication with an **EBO** and 24 unique vertices (positions, normals, uvs) if you also need per-face normals.
>     
> - **Textures**: bind before draw; if using multiple, assign sampler units with `glUniform1i`.
>     
> - **Resize**: on framebuffer size change, call `glViewport` and rebuild `projection` with the new aspect ratio.
>