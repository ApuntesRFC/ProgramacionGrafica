> [!summary]  
> **Texture units** let you bind and sample **multiple textures** in one draw.  
> Set each sampler to a unit with `glUniform1i`, activate units with `glActiveTexture`, and bind textures to those units.

---

### Samplers and texture units

- `sampler2D` is a **uniform** that refers to a **texture unit** index.
    
- Unit `GL_TEXTURE0` is often active by default. Some drivers need explicit binding.
    

```glsl
// Fragment shader
#version 330 core
in vec2 TexCoord;
out vec4 FragColor;

uniform sampler2D texture1;
uniform sampler2D texture2;

void main() {
    FragColor = mix(texture(texture1, TexCoord),
                    texture(texture2, TexCoord), 0.2); // 80% tex1, 20% tex2
}
```

---

### CPU setup: load, bind, and assign units

```cpp
// 1) Load images (PNG with alpha uses GL_RGBA)
int w,h,c;
unsigned int tex1, tex2;

stbi_set_flip_vertically_on_load(true);

unsigned char* d1 = stbi_load("container.jpg", &w,&h,&c, 0);
glGenTextures(1, &tex1);
glBindTexture(GL_TEXTURE_2D, tex1);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, w, h, 0, GL_RGB, GL_UNSIGNED_BYTE, d1);
glGenerateMipmap(GL_TEXTURE_2D);
stbi_image_free(d1);

unsigned char* d2 = stbi_load("awesomeface.png", &w,&h,&c, 0);
glGenTextures(1, &tex2);
glBindTexture(GL_TEXTURE_2D, tex2);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, w, h, 0, GL_RGBA, GL_UNSIGNED_BYTE, d2);
glGenerateMipmap(GL_TEXTURE_2D);
stbi_image_free(d2);
```

---

### One-time sampler-to-unit binding

```cpp
shader.use();
glUniform1i(glGetUniformLocation(shader.ID, "texture1"), 0); // sampler texture1 → unit 0
glUniform1i(glGetUniformLocation(shader.ID, "texture2"), 1); // sampler texture2 → unit 1
// or via helper: shader.setInt("texture2", 1);
```

---

### Per-frame draw

```cpp
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, tex1);

glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, tex2);

glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

---

### Notes

> [!tip]
> 
> - Query max units if needed: `glGetIntegerv(GL_MAX_COMBINED_TEXTURE_IMAGE_UNITS, &n);` (min 16).
>     
> - Use `GL_RGB` vs `GL_RGBA` to match channel count.
>     
> - If you see white/black, ensure: active program set, samplers assigned to units, texture bound to the same units, and formats correct.
>