> [!summary]  
> Creating a texture in OpenGL involves **generating**, **binding**, **configuring**, **uploading**, and **freeing** the image data. Once uploaded, the texture resides on the GPU.

---

### 1. Generate and bind the texture

```cpp
unsigned int texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
```

- `glGenTextures(n, &id)` → creates one or more texture objects.
    
- `glBindTexture(target, id)` → makes this texture the active one for configuration.
    

---

### 2. Set wrapping and filtering parameters

```cpp
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

> [!info]
> 
> - Wrapping: how UVs outside `[0, 1]` are handled.
>     
> - Filtering: how texels are sampled when scaling up or down.
>     
> - Always set parameters _after binding_ the texture.
>     

---

### 3. Load image data from disk

```cpp
int width, height, nrChannels;
unsigned char* data = stbi_load("container.jpg", &width, &height, &nrChannels, 0);
if (!data) {
    std::cerr << "Failed to load texture" << std::endl;
}
```

- Uses **stb_image.h** for decoding image formats (JPEG, PNG, etc.).
    
- `nrChannels` helps decide format (`GL_RGB` or `GL_RGBA`).
    

---

### 4. Upload to GPU and generate mipmaps

```cpp
if (data) {
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0,
                 GL_RGB, GL_UNSIGNED_BYTE, data);
    glGenerateMipmap(GL_TEXTURE_2D);
}
```

|Parameter|Meaning|
|---|---|
|`GL_TEXTURE_2D`|Texture target (2D).|
|`0`|Mipmap level (base).|
|`GL_RGB`|Internal format (GPU storage).|
|`width`, `height`|Dimensions.|
|`0`|Always 0 (legacy).|
|`GL_RGB`|Format of the source image.|
|`GL_UNSIGNED_BYTE`|Data type.|
|`data`|Pointer to pixel data.|

> [!tip]  
> For PNGs or textures with alpha:
> 
> ```cpp
> GLenum format = (nrChannels == 4) ? GL_RGBA : GL_RGB;
> glTexImage2D(GL_TEXTURE_2D, 0, format, width, height, 0, format, GL_UNSIGNED_BYTE, data);
> ```

---

### 5. Free CPU memory

```cpp
stbi_image_free(data);
```

> Once the texture is on the GPU, the original pixel data is no longer needed.

---

### 6. Summary

> [!important]  
> ✅ Steps overview:
> 
> 1. `glGenTextures` → create
>     
> 2. `glBindTexture` → activate
>     
> 3. `glTexParameteri` → configure wrapping/filtering
>     
> 4. `glTexImage2D` → upload image
>     
> 5. `glGenerateMipmap` → build mipmap chain
>     
> 6. `stbi_image_free` → release CPU memory
>     

You now have a texture ready for rendering and can bind it during draw calls using:

```cpp
glBindTexture(GL_TEXTURE_2D, texture);
```