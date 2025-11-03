> [!summary]  
> Textures are **2D images mapped onto 3D surfaces** using **texture coordinates (UVs)**. Each vertex gets a `(u, v)` coordinate in the `[0,1]` range, telling OpenGL **which part of the image** to sample for that fragment.

---

### Concept

A **texture** = image data stored on the GPU.  
Each **vertex** of your model includes **texture coordinates** → the fragment shader uses them to **sample** the image (via interpolation).

---

### Example: triangle with texture coordinates

```cpp
float vertices[] = {
    // positions        // colors         // texture coords
     0.5f, -0.5f, 0.0f, 1.0f, 0.0f, 0.0f,  1.0f, 0.0f,   // bottom right
    -0.5f, -0.5f, 0.0f, 0.0f, 1.0f, 0.0f,  0.0f, 0.0f,   // bottom left
     0.0f,  0.5f, 0.0f, 0.0f, 0.0f, 1.0f,  0.5f, 1.0f    // top center
};
```

> Each vertex has 8 floats: 3 (position) + 3 (color) + 2 (texture coordinate).

---

### Texture coordinates (UV mapping)

|Corner|Texture coordinate|
|---|--:|
|Lower-left|`(0.0, 0.0)`|
|Lower-right|`(1.0, 0.0)`|
|Upper-center|`(0.5, 1.0)`|

Texture coordinates always go from

- **(0, 0)** → bottom-left of image
    
- **(1, 1)** → top-right of image
    

---

### Vertex attribute setup

```cpp
// position
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// color
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);

// texture coords
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float)));
glEnableVertexAttribArray(2);
```

---

### Vertex Shader

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;
layout (location = 2) in vec2 aTexCoord;

out vec3 ourColor;
out vec2 TexCoord;

void main() {
    gl_Position = vec4(aPos, 1.0);
    ourColor = aColor;
    TexCoord = aTexCoord;
}
```

### Fragment Shader

```glsl
#version 330 core
out vec4 FragColor;

in vec3 ourColor;
in vec2 TexCoord;

uniform sampler2D ourTexture;

void main() {
    FragColor = texture(ourTexture, TexCoord) * vec4(ourColor, 1.0);
}
```

---

### Sampling process

> [!info]  
> The **fragment shader** samples the texture using:
> 
> ```glsl
> texture(ourTexture, TexCoord);
> ```
> 
> OpenGL interpolates `(u,v)` coordinates between vertices → each pixel automatically gets the correct sample color.

---

### Key idea

Textures replace per-vertex color detail with **one high-resolution image**, making objects look **realistic** with minimal geometry cost.