
> [!summary]
Pixels and coordinates in computer graphics: how discrete image elements represent continuous reality, and how aliasing, antialiasing, and resolution define image quality.

---

### Pixel and Coordinates

A **pixel** is identified by its **row and column number**, which act as discrete addresses in an image grid rather than continuous mathematical points.  

Each pixel represents a small area in the continuous plane.

> [!note]
> - A pixel is not a single point — it covers infinitely many points theoretically but is represented as a discrete square.  
> - The **goal of computer graphics** is not just coloring pixels, but creating and manipulating digital approximations of continuous reality.

---

### Example of Pixel Coordinates


| Coordinate (x,y) | OpenGL Row Numbering | Screen Position  |
|------------------|----------------------|------------------|
| (0,0)            | Bottom row           | Bottom-left pixel|
| (2,0)            | Bottom row           | 3rd pixel on row |
| (0,2)            | Row 2 from bottom    | Left pixel       |


> [!info]  
> OpenGL uses **bottom-to-top row numbering**, while some image processing systems number **top-to-bottom**.

---

### Pixels as Approximation

Pixels are **approximations** of continuous geometry and color.  
Real shapes and curves exist in continuous space, but when represented by discrete pixels, visual errors arise.

> [!note]  
> This limitation is inherent to all raster graphics: finite resolution cannot capture infinite detail.

---

### Aliasing

**Aliasing** appears when fine detail cannot be accurately represented at the current pixel resolution.

It produces visible artifacts such as:

- Jagged or stair-stepped edges (“jaggies”)
    
- Moiré interference patterns
    
- Flickering in small moving details
    

> [!tip]  
> Aliasing results from **undersampling** — too few pixels for the frequency of detail in the scene.

---

### Antialiasing

**Antialiasing** reduces aliasing artifacts by blending colors at object boundaries.  
Instead of assigning a pixel fully to one color, intermediate shades are used to smooth visual transitions.

> [!example]
> 
> - A diagonal line may use gray pixels along its edge to appear smoother to the eye.
>     

> [!note]  
> Antialiasing does not add detail; it simply improves **visual perception** by averaging edge transitions.
> Website to test antialiasing: [Pixel Magnifier](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c2/pixel-magnifier.html)

---

### Other Issues in Mapping Coordinates to Pixels

> [!info]
> 
> - **Reference point ambiguity:** Does `(x, y)` refer to the pixel center or corner? Most systems use the **center**, but conventions vary.
>     
> - **Coordinate density:** Defining every pixel’s coordinate directly is inefficient for large images.
>     
> - **Abstract pixels:** In UI design or web development, “pixel” often refers to a **logical unit**, not a physical pixel.
>     

---

### DPI and PPI

> [!info]
> 
> - **DPI (Dots Per Inch):** number of ink dots per inch on a printer.
>     
> - **PPI (Pixels Per Inch):** pixel density on a display.
>     

> [!tip]  
> Higher PPI produces **smoother images** and finer visual detail, though it increases memory and computation requirements.

---

### Vector Graphics vs Raster Graphics

Raster images are **discrete approximations** of continuous objects, while vector graphics represent objects mathematically.

> [!note]
> 
> - Rasterization of vector graphics reintroduces approximation errors when displayed or printed.
>     
> - Higher resolution mitigates visible artifacts but cannot eliminate them entirely.
>     

---

### Key Idea

A rasterized image is **always an approximation** of a continuous model.  
Aliasing and resolution limitations are unavoidable, but **antialiasing** and **high pixel density** minimize perceptible errors, bridging the gap between digital and continuous representation.