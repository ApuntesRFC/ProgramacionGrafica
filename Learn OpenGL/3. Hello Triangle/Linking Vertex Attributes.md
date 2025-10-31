> [!summary]  
> You must tell OpenGL **how your VBO data maps to vertex shader attributes**. Use `glVertexAttribPointer` to describe layout and `glEnableVertexAttribArray` to activate it. Attribute index must match the shader’s `layout(location=…)`.

---

### Attribute layout → `layout(location = 0) in vec3 aPos;`

```cpp
// VBO already filled with positions: [-0.5f, -0.5f, 0.0f,  ...]

// 1) Bind the source VBO for attribute setup
glBindBuffer(GL_ARRAY_BUFFER, VBO);

// 2) Describe how to read one vertex (vec3 float) from the bound VBO
glVertexAttribPointer(
    0,                    // attribute index → matches 'layout(location=0)'
    3,                    // size → 3 components per vertex
    GL_FLOAT,             // type → floats
    GL_FALSE,             // normalize integers? not needed for floats
    3 * sizeof(float),    // stride → bytes to the next vertex
    (void*)0              // offset → start of the position data in the buffer
);

// 3) Enable the attribute
glEnableVertexAttribArray(0);
```

---

### Parameters decoded

|Param|What it is|For this triangle|
|---|---|---|
|`index`|Shader attribute slot|`0`|
|`size`|Components per vertex|`3` (x,y,z)|
|`type`|Component type|`GL_FLOAT`|
|`normalized`|Integer-to-float normalization|`GL_FALSE`|
|`stride`|Byte distance to next vertex|`3*sizeof(float)`|
|`pointer`|Byte offset of first element|`(void*)0`|

> [!note]  
> The **currently bound VBO to `GL_ARRAY_BUFFER`** at the time of `glVertexAttribPointer` becomes the data source for that attribute.

---

### Minimal draw flow (no VAO yet)

```cpp
// 0) Upload vertex data
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 1) Define attribute 0
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3*sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// 2) Use shaders
glUseProgram(shaderProgram);

// 3) Draw
glDrawArrays(GL_TRIANGLES, 0, 3);
```

> [!warning]  
> Repeating this setup for many attributes and many objects is tedious and error-prone.

---

### High-leverage solution preview: **VAO**

> [!important]  
> A **Vertex Array Object (VAO)** records all attribute bindings and layouts.  
> Later you will:
> 
> 1. `glGenVertexArrays`, `glBindVertexArray(vao)`
>     
> 2. Run the attribute setup once
>     
> 3. During rendering, just bind the VAO and call `glDrawArrays`  
>     This collapses lots of state calls into a single bind.
>