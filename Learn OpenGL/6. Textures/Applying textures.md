> [!summary]  
> Add **UVs** to your rectangle, update **VAO attribute 2**, pass UVs **VS → FS**, and **sample** with a `sampler2D`. Bind the texture before `glDrawElements`. If it renders white/black, set the **sampler’s texture unit**.

---

### Vertex data with UVs

```cpp
// pos(3) color(3) uv(2)  → stride = 8 floats
float vertices[] = {
    // positions        // colors        // texcoords
     0.5f,  0.5f, 0.0f,  1.0f,0.0f,0.0f,  1.0f,1.0f,  // top right
     0.5f, -0.5f, 0.0f,  0.0f,1.0f,0.0f,  1.0f,0.0f,  // bottom right
    -0.5f, -0.5f, 0.0f,  0.0f,0.0f,1.0f,  0.0f,0.0f,  // bottom left
    -0.5f,  0.5f, 0.0f,  1.0f,1.0f,0.0f,  0.0f,1.0f   // top left
};
```

```cpp
// VAO layout
const GLsizei stride = 8 * sizeof(float);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, stride, (void*)0);                      glEnableVertexAttribArray(0); // pos
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, stride, (void*)(3 * sizeof(float)));    glEnableVertexAttribArray(1); // color
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, stride, (void*)(6 * sizeof(float)));    glEnableVertexAttribArray(2); // uv
```

> [!note]  
> Update the **stride for attributes 0 and 1** to `8*sizeof(float)`.

---

### Shaders

**Vertex shader**

```glsl
#version 330 core
layout (location=0) in vec3 aPos;
layout (location=1) in vec3 aColor;
layout (location=2) in vec2 aTexCoord;

out vec3 ourColor;
out vec2 TexCoord;

void main() {
    gl_Position = vec4(aPos, 1.0);
    ourColor = aColor;
    TexCoord = aTexCoord;
}
```

**Fragment shader**

```glsl
#version 330 core
in vec3 ourColor;
in vec2 TexCoord;
out vec4 FragColor;

uniform sampler2D ourTexture;

void main() {
    FragColor = texture(ourTexture, TexCoord);                 // pure texture
    // FragColor = texture(ourTexture, TexCoord) * vec4(ourColor, 1.0); // mix with vertex color
}
```

---

### Draw

```cpp
// bind texture + VAO, then draw
glBindTexture(GL_TEXTURE_2D, texture);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

---

### If you see white/black: set the sampler’s texture unit

Some drivers require explicit unit binding.

```cpp
// once at init, after linking program
glUseProgram(shaderProgram);
glUniform1i(glGetUniformLocation(shaderProgram, "ourTexture"), 0); // sampler → unit 0

// before draw each frame
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, texture);
```

> [!tip]  
> Ensure the texture was uploaded with the **correct format** (`GL_RGB` vs `GL_RGBA`) and that mipmap/filter settings match your usage.
