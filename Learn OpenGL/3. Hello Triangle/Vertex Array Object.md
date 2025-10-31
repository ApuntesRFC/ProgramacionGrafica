> [!summary]  
> A **Vertex Array Object (VAO)** stores all vertex attribute configurations and the bindings of VBOs used by those attributes.  
> In modern OpenGL (Core Profile), **a VAO is mandatory** — if no VAO is bound, nothing will render.

---

### Step 1 — Generate and Bind a VAO

```cpp
unsigned int VAO;
glGenVertexArrays(1, &VAO);
glBindVertexArray(VAO);
```

> [!info]
> 
> - Similar to how `glGenBuffers` creates VBOs.
>     
> - Once bound, **all vertex attribute calls and buffer associations** are recorded inside this VAO.
>     

---

### Step 2 — Configure the VBO and Attributes (once)

```cpp
// Bind and fill vertex buffer
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// Define vertex attribute layout
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```

> [!note]  
> VAOs remember:
> 
> - Which attributes are enabled.
>     
> - How to interpret vertex data (`glVertexAttribPointer`).
>     
> - Which VBOs were used for each attribute when configured.
>     

---

### Step 3 — Unbind for safety (optional)

```cpp
glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0);
```

---

### Step 4 — Draw Using the VAO

Inside your **render loop**:

```cpp
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);  // draw 3 vertices → 1 triangle
```

> [!tip]  
> You only need to call `glBindVertexArray(VAO)` again before drawing.  
> The VAO restores all vertex attribute state instantly.

---

### Concept — What a VAO Stores

|Stored in VAO|Description|
|---|---|
|`glEnableVertexAttribArray` / `glDisableVertexAttribArray`|Which attributes are active|
|`glVertexAttribPointer` calls|Attribute layout, type, stride, offset|
|Bound VBOs|Which buffer each attribute pulls data from|

> [!important]  
> **VAOs encapsulate vertex state**, letting you configure once and reuse it.  
> Without them, you’d have to redo every buffer and attribute setup per frame.

---

### Step 5 — Typical Usage Pattern

> [!example]
> 
> ```cpp
> // Initialization (once)
> glGenVertexArrays(1, &VAO);
> glGenBuffers(1, &VBO);
> 
> glBindVertexArray(VAO);
> glBindBuffer(GL_ARRAY_BUFFER, VBO);
> glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
> 
> glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
> glEnableVertexAttribArray(0);
> 
> glBindBuffer(GL_ARRAY_BUFFER, 0);
> glBindVertexArray(0);
> 
> // Render loop
> glUseProgram(shaderProgram);
> glBindVertexArray(VAO);
> glDrawArrays(GL_TRIANGLES, 0, 3);
> ```

---

### Step 6 — Key Takeaways

> [!important]
> 
> - **VAO = Vertex attribute configuration container.**
>     
> - Required in Core Profile (3.3+).
>     
> - Greatly simplifies switching between multiple objects.
>     
> - Always bind the VAO before drawing.
>