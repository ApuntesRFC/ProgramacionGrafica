> [!summary]  
> **Shader inputs and outputs** (`in` / `out`) allow data to flow through the **graphics pipeline**.  
> Each shader passes results to the next stage by matching **name and type** between variables.

---

### Shader data flow

> [!info]
> 
> - The **vertex shader** receives input from your **vertex attributes** (VBOs).
>     
> - The **fragment shader** outputs a final **color** for each pixel.
>     
> - Data moves between stages via **matching `out` / `in` variables**.
>     

```
Vertex Data → Vertex Shader → Fragment Shader → Color Buffer
```

---

### Vertex shader: define output

```glsl
#version 330 core

layout (location = 0) in vec3 aPos;   // vertex position attribute
out vec4 vertexColor;                 // output sent to fragment shader

void main()
{
    gl_Position = vec4(aPos, 1.0);        // transform position
    vertexColor = vec4(0.5, 0.0, 0.0, 1.0); // dark red
}
```

> [!note]
> 
> - The keyword `layout(location = 0)` links this input with your CPU-side vertex attribute (VBO).
>     
> - The `out` variable **passes data** to the next stage.
>     

---

### Fragment shader: receive input and output color

```glsl
#version 330 core

in vec4 vertexColor;   // receives data from vertex shader
out vec4 FragColor;    // final color output

void main()
{
    FragColor = vertexColor; // use the passed color
}
```

> [!important]  
> The **fragment shader must define an output color** (`out vec4`) or OpenGL’s output is undefined (often black or white).

---

### Linking between shaders

> [!info]  
> During **program linking** (`glLinkProgram`), OpenGL automatically matches  
> any `out` variable in one shader with an `in` variable of the same **name** and **type** in the next shader.

|From (Vertex Shader)|To (Fragment Shader)|
|---|---|
|`out vec4 vertexColor;`|`in vec4 vertexColor;`|

---

### Alternate approach

You could query attribute locations instead of setting them manually:

```cpp
int posAttrib = glGetAttribLocation(shaderProgram, "aPos");
```

…but using explicit layout specifiers in GLSL is **simpler and clearer**, since it avoids runtime queries.

---

### Minimal OpenGL-side summary

```cpp
// link shader program as usual
glUseProgram(shaderProgram);

// VBO setup (as before)
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);
```

This will draw a **dark red triangle**, because the vertex shader passes a color directly to the fragment shader.

---

### Key takeaways

> [!important]
> 
> - Data flows between shaders through **matching `in`/`out` pairs**.
>     
> - The **vertex shader** outputs variables like position, color, or texture coordinates.
>     
> - The **fragment shader** consumes them and must produce a final **`vec4` color**.
>     
> - Explicit `layout(location = x)` simplifies CPU–GPU linking.
>     
> - Always define `out vec4 FragColor` in your fragment shader for valid rendering.
>