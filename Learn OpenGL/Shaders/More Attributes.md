> [!summary]  
> Add a **color attribute** to each vertex, update the **shaders** to pass color from VS → FS, and configure **two vertex attributes** in the VAO. Colors smoothly appear via **fragment interpolation**.

---

### Vertex data: positions + colors

```cpp
// 6 floats per vertex: 3 pos + 3 color
float vertices[] = {
    // positions         // colors (RGB)
     0.5f, -0.5f, 0.0f,   1.0f, 0.0f, 0.0f,  // bottom right → red
    -0.5f, -0.5f, 0.0f,   0.0f, 1.0f, 0.0f,  // bottom left  → green
     0.0f,   0.5f, 0.0f,   0.0f, 0.0f, 1.0f   // top         → blue
};
```

---

### Shaders

**Vertex Shader**

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;    // position
layout (location = 1) in vec3 aColor;  // color
out vec3 ourColor;                     // to fragment shader

void main() {
    gl_Position = vec4(aPos, 1.0);
    ourColor = aColor;
}
```

**Fragment Shader**

```glsl
#version 330 core
in vec3 ourColor;
out vec4 FragColor;

void main() {
    FragColor = vec4(ourColor, 1.0);
}
```

---

### VAO attribute setup

```cpp
glBindVertexArray(VAO);

// VBO already bound and filled with `vertices`
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

const GLsizei stride = 6 * sizeof(float);

// position attribute (location = 0)
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, stride, (void*)0);
glEnableVertexAttribArray(0);

// color attribute (location = 1), starts after 3 floats
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, stride, (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);
```

> [!info]  
> **Stride** = bytes from one vertex to the next = `6 * sizeof(float)`.  
> **Offset** for color = `3 * sizeof(float)` because color follows position.

---

### Draw

```cpp
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);
```

---

### Why you see a gradient: **fragment interpolation**

> [!note]  
> Rasterization produces many fragments per triangle. Inputs to the fragment shader (like `ourColor`) are **linearly interpolated** across the triangle based on fragment position.  
> Three vertex colors → a smooth color blend over ~thousands of fragments.

---

### Quick checklist

- Attribute locations match shaders: `0 → aPos`, `1 → aColor`.
    
- VAO was **bound** when calling both `glVertexAttribPointer` and `glEnableVertexAttribArray`.
    
- `glUseProgram(shaderProgram)` is active before drawing.
    
- Clear each frame; draw after setting program and VAO.