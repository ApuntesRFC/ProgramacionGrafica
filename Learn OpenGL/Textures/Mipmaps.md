> [!summary]  
> **Mipmaps** are precomputed, progressively smaller versions of a texture.  
> OpenGL automatically chooses which mipmap level to sample based on **distance** and **object size**, improving **performance** and **visual quality**.

---

### Concept

A **mipmap chain** = the original texture + several downscaled versions, each half the previous size.

|Level|Resolution|Description|
|---|---|---|
|0|1024×1024|Original high-res texture|
|1|512×512|Half-size|
|2|256×256|Quarter-size|
|3|…|… until 1×1|

> The GPU automatically switches to lower levels as objects move farther away.

---

### Benefits

- Removes **aliasing artifacts** on distant objects.
    
- Improves **texture sampling accuracy**.
    
- Saves **memory bandwidth** (uses smaller textures for small fragments).
    
- GPU can **cache** texels more efficiently.
    

---

### Creating mipmaps

```cpp
glBindTexture(GL_TEXTURE_2D, textureID);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0,
             GL_RGB, GL_UNSIGNED_BYTE, data);
glGenerateMipmap(GL_TEXTURE_2D);
```

> [!info]  
> `glGenerateMipmap()` automatically builds the full mipmap chain after uploading your base texture.

---

### Filtering with mipmaps

You can control how OpenGL samples **between mipmap levels**:

|Mode|Description|
|---|---|
|`GL_NEAREST_MIPMAP_NEAREST`|Chooses the nearest mipmap and uses nearest texel.|
|`GL_LINEAR_MIPMAP_NEAREST`|Chooses the nearest mipmap and samples with linear filtering.|
|`GL_NEAREST_MIPMAP_LINEAR`|Interpolates between two nearest mipmaps, using nearest sampling.|
|`GL_LINEAR_MIPMAP_LINEAR`|Interpolates between two nearest mipmaps and uses linear filtering on both. _(best quality)_|

---

### Example configuration

```cpp
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

> [!warning]
> 
> - **Mipmaps only apply to minification** (`GL_TEXTURE_MIN_FILTER`).
>     
> - Using mipmap filters for magnification (`GL_TEXTURE_MAG_FILTER`) triggers **`GL_INVALID_ENUM`**.
>     

---

### Summary

> [!important]
> 
> - Use `glGenerateMipmap` after loading a texture.
>     
> - Set `GL_LINEAR_MIPMAP_LINEAR` for high-quality downscaling.
>     
> - Mipmaps are critical for smooth, efficient texture rendering in 3D scenes with many objects.
>