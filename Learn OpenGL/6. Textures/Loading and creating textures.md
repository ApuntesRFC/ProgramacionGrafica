> [!summary]  
> Use **stb_image.h** to load textures from disk into CPU memory, then upload to OpenGL with `glTexImage2D`. Define `STB_IMAGE_IMPLEMENTATION` **once** in a `.cpp`.

---

### Setup

1. Download: `https://github.com/nothings/stb/blob/master/stb_image.h`
    
2. Create a single implementation file:
    

```cpp
// stb_image_impl.cpp
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```

3. Include where needed:
    

```cpp
#include "stb_image.h"
```

> [!tip]  
> Define `STB_IMAGE_IMPLEMENTATION` in **exactly one** translation unit.

---

### Load an image

```cpp
int width, height, nrChannels;

// Optional: flip so UV (0,0) is bottom-left
// stbi_set_flip_vertically_on_load(true);

unsigned char* data = stbi_load("container.jpg", &width, &height, &nrChannels, 0);
if (!data) {
    std::cerr << "Failed to load image\n";
    // handle error...
}
```

- `width`, `height` → needed for texture creation.
    
- `nrChannels` → 3 for RGB, 4 for RGBA, etc.
    

---

### Upload to OpenGL (minimal)

```cpp
GLuint tex; 
glGenTextures(1, &tex);
glBindTexture(GL_TEXTURE_2D, tex);

// Wrapping
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);

// Filtering
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

// Pick formats based on channels
GLenum format = (nrChannels == 4) ? GL_RGBA : GL_RGB;

// Upload base level and build mipmaps
glTexImage2D(GL_TEXTURE_2D, 0, format, width, height, 0, format,
             GL_UNSIGNED_BYTE, data);
glGenerateMipmap(GL_TEXTURE_2D);

// Free CPU pixels
stbi_image_free(data);
```

> [!note]  
> Bind with `glActiveTexture(GL_TEXTURE0 + unit)` and set your sampler uniform to that unit.

---

### Common pitfalls

- Unused sampler uniform → make sure you set it and **use the program** before drawing.
    
- Wrong format pair (e.g., sending RGBA data with `GL_RGB`) → corrupted colors.
    
- Missing mipmaps while using a mipmap min filter → texture may render black.
    
- Forgot to flip images → textures appear upside down.