> [!summary]
Basic foundations of computer graphics rely on two pillars: **coordinate systems** and **color models**. Color defines how light intensities combine to form the visible image.

---

### Light and Color

Light consists of electromagnetic waves with different **wavelengths**.  
A **pure color** has a single wavelength, while most colors are mixtures of many.

> [!note]
> - In reality, colors form a **continuous spectrum** of infinite possible wavelengths.  
> - Digital devices can only reproduce a limited subset of these — the **color gamut**.

---

### RGB Color Model

Computer screens generate colors by combining **red (R)**, **green (G)**, and **blue (B)** light at varying intensities between `0` and `1`.

> [!info]
> - Each pixel stores three components: `(R, G, B)`  
> - Example:  
>   - `(1, 0, 0)` → pure red  
>   - `(0, 1, 0)` → pure green  
>   - `(0, 0, 1)` → pure blue  
>   - `(1, 1, 1)` → white  
>   - `(0, 0, 0)` → black

> [!example]
> RGB values are usually stored using **8 bits per component**, giving  
> $256 \times 256 \times 256 = 16,777,216$ possible colors (24-bit color).

---

### Color Depth and Precision

> [!info]
> - **Standard depth:** 8 bits per component (24 bits per pixel).  
> - **High depth:** 16 or 32 bits per component to avoid rounding or banding effects.  
> - **Reduced depth:** 15 bits per pixel (5 bits red, 5 bits blue, 6 bits green).  
>   - Green gets one extra bit because the human eye is most sensitive to green.  
>   - Used historically to **save memory**.

---

### Color Gamut

Different devices can reproduce different subsets of all visible colors.

> [!note]
> - **Screen gamut (RGB):** based on additive color mixing (light).  
> - **Printer gamut (CMYK):** based on subtractive color mixing (ink).  
> - The same `(R,G,B)` values may produce **different colors** across devices.

> [!tip]
> The **color gamut** defines the range of colors a device can display or print.  
> Some real-world colors lie **outside** any device’s gamut — they simply can’t be shown digitally.

---

### Alternative Color Models

The **RGB model** is hardware-based but not intuitive for describing color perception.  
Alternative models describe colors in more human-friendly ways:

#### HSV (Hue, Saturation, Value)
> [!info]
> - **Hue (H):** the color type (angle on color wheel, 0–360°).  
> - **Saturation (S):** color intensity (0 = gray, 1 = pure).  
> - **Value (V):** brightness (0 = black, 1 = full color).

#### HSL (Hue, Saturation, Lightness)
> [!info]
> - **Hue (H):** same as HSV.  
> - **Saturation (S):** color purity.  
> - **Lightness (L):** controls how light or dark the color appears (0 = black, 0.5 = pure, 1 = white).

> [!example]
> Visual comparison (interactive): [RGB ↔ HSV Color Demo](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/rgb-hsv.html)

---

### Alpha Channel and Transparency

The **alpha component** adds transparency information to colors.  
It defines how the **foreground color** blends with the **background**.

$$
\text{new color} = \alpha \cdot \text{foreground} + (1 - \alpha) \cdot \text{background}
$$

> [!info]
> - $\alpha = 1$ → fully opaque  
> - $\alpha = 0$ → fully transparent  
> - Intermediate values create **semi-transparent** effects.

---

### RGBA and ARGB Formats

> [!note]
> - **RGBA (Red, Green, Blue, Alpha):** 32-bit representation (8 bits each).  
> - **ARGB:** same data but stored with **Alpha first** (bit order `A R G B`).

> [!tip]
> The alpha channel enables **blending**, **fading**, and **compositing** in 2D/3D graphics, forming the basis of transparency effects in modern rendering pipelines.

---

### Key Idea

Color in computer graphics is a **numerical approximation** of physical light.  
RGB models real-world emission, HSV and HSL model human perception, and **alpha blending** controls how colors combine when overlapping in digital scenes.
