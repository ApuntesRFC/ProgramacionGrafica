> [!summary]  
> **Element Buffer Objects (EBOs)** enable **indexed drawing**. You store unique vertices once in a VBO and an **index list** in an EBO. Draw with `glDrawElements`. Bind the EBO while a **VAO** is bound so the VAO remembers it.

---

### Why EBOs

- Rectangles and real meshes repeat vertices.
    
- EBO stores **indices** so repeated corners are **referenced**, not duplicated.
    
- Less memory, better cache, cleaner topology.
    

---

### Vertex + index data

```cpp
// unique positions (VBO)
float vertices[] = {
     0.5f,  0.5f, 0.0f,  // top-right
     0.5f, -0.5f, 0.0f,  // bottom-right
    -0.5f, -0.5f, 0.0f,  // bottom-left
    -0.5f,  0.5f, 0.0f   // top-left
};

// element indices (EBO)
unsigned int indices[] = {
    0, 1, 3,  // first triangle
    1, 2, 3   // second triangle
};
```

---

### Create and fill the EBO

```cpp
unsigned int EBO;
glGenBuffers(1, &EBO);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```

> [!note]  
> Target is **`GL_ELEMENT_ARRAY_BUFFER`**.

---

### Bind order with VAO (record once)

```cpp
glBindVertexArray(VAO);                          // record state into this VAO

glBindBuffer(GL_ARRAY_BUFFER, VBO);              // vertex data
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);      // index data (VAO will remember this binding)

glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3*sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// optional safety, but DO NOT unbind the element array while VAO is bound
glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0);
```

> [!warning]  
> A VAO **stores** the `GL_ELEMENT_ARRAY_BUFFER` binding.  
> Unbinding the EBO **before** unbinding the VAO clears the association.

---

### Draw with indices

```cpp
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);  // 6 indices → 2 triangles
```

|Param|Meaning|Here|
|---|---|---|
|`mode`|Primitive type|`GL_TRIANGLES`|
|`count`|Number of indices to read|`6`|
|`type`|Index type|`GL_UNSIGNED_INT`|
|`indices`|Offset into EBO when bound, or client pointer if no EBO|`(void*)0`|

> [!tip]  
> `count` = index count, **not** vertex count.

---

### Wireframe toggle (debug)

```cpp
glPolygonMode(GL_FRONT_AND_BACK, GL_LINE); // wireframe on
// ... draw ...
glPolygonMode(GL_FRONT_AND_BACK, GL_FILL); // back to solid
```

---

### Minimal end-to-end snippet

```cpp
// VAO/VBO/EBO setup
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);
glGenBuffers(1, &EBO);

glBindVertexArray(VAO);

glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3*sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

glBindVertexArray(0);

// render loop
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

---

### Key takeaways

> [!important]
> 
> - **EBO = indices**; **VBO = unique vertices**; **VAO = layout + bindings**.
>     
> - Bind EBO while VAO is bound so the VAO remembers it.
>     
> - Use `glDrawElements` for indexed rendering.
>