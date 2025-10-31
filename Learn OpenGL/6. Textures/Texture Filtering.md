> [!summary]  
> **Texture filtering** controls how OpenGL samples texels when a texture is scaled up or down.  
> You configure it using `GL_TEXTURE_MIN_FILTER` (for downscaling) and `GL_TEXTURE_MAG_FILTER` (for upscaling).

---

### Filtering modes

|Constant|Description|Visual result|
|---|---|---|
|`GL_NEAREST`|Chooses the texel whose **center** is closest to the texture coordinate.|Pixelated, blocky (retro 8-bit look).|
|`GL_LINEAR`|Interpolates between the **4 nearest texels** to get a blended result.|Smooth, softer transitions.|

---

### Example configuration

```cpp
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

> [!info]
> 
> - **`GL_TEXTURE_MIN_FILTER`** → used when the texture is scaled _down_ (minified).
>     
> - **`GL_TEXTURE_MAG_FILTER`** → used when the texture is scaled _up_ (magnified).
>     
> - Each can be configured independently.
>     

---

### Conceptual difference

|Operation|GL_NEAREST|GL_LINEAR|
|---|---|---|
|**Minifying**|picks 1 texel|averages nearby texels|
|**Magnifying**|repeats 1 texel|blends multiple texels|

---

### Summary

> [!important]
> 
> - Filtering defines **how texture coordinates map to texels**.
>     
> - `GL_NEAREST`: crisp pixels, faster.
>     
> - `GL_LINEAR`: smoother gradients, more realistic.
>     
> - Use both modes to control the balance between **speed** and **visual quality**.
>