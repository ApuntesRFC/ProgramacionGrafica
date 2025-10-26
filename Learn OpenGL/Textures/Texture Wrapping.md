> [!summary]  
> **Texture wrapping** defines how OpenGL samples textures when coordinates fall **outside the [0, 1] range**.  
> The behavior is controlled **per-axis (S, T, R)** with `glTexParameteri`.

---

### Wrapping modes

|Constant|Behavior|Description|
|---|---|---|
|`GL_REPEAT`|🔁|Default. Ignores integer part of coordinates. The texture repeats infinitely.|
|`GL_MIRRORED_REPEAT`|↔️|Same as repeat, but every other repetition is mirrored.|
|`GL_CLAMP_TO_EDGE`|⛔|Clamps coordinates between 0 and 1 → texture edges stretch.|
|`GL_CLAMP_TO_BORDER`|🟨|Outside [0, 1] returns a **border color** you specify.|

---

### Example configuration

```cpp
// Assume a 2D texture is bound to GL_TEXTURE_2D
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRRORED_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_MIRRORED_REPEAT);
```

> [!info]
> 
> - `GL_TEXTURE_WRAP_S` → x-axis (U)
>     
> - `GL_TEXTURE_WRAP_T` → y-axis (V)
>     
> - `GL_TEXTURE_WRAP_R` → z-axis (for 3D textures)
>     

---

### Border color example (`GL_CLAMP_TO_BORDER`)

```cpp
float borderColor[] = { 1.0f, 1.0f, 0.0f, 1.0f }; // yellow border
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_BORDER);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_BORDER);
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);
```

---

### Summary

> [!important]
> 
> - Wrapping modes define **how UVs outside [0, 1] are handled**.
>     
> - Always call these _after binding_ the texture.
>     
> - Typical workflow:
>     
>     1. `glBindTexture(GL_TEXTURE_2D, texID);`
>         
>     2. Configure wrapping (and filtering).
>         
>     3. Load texture data (`glTexImage2D`, etc.).
>         
>     4. Generate mipmaps if needed.
>