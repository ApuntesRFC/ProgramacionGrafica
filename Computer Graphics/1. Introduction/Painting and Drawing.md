> [!summary]
Fundamentals of digital imaging: pixels, color representation, raster and vector models, frame buffers, compression methods, and coordinate systems.

---

### Pixels

A **pixel** (picture element) is the smallest addressable unit in a digital image or on a screen. Each pixel represents a small square of color or light. When you zoom into a photo, you can observe the grid of pixels that form the image. Each pixel generally stores color data, for example, in **RGB format** (red, green, blue).

> [!example]
> - Analogy: Pixels are like tiles in a mosaic. One tile does not show the full picture, but many together form an image.

> [!note]
> - The quality of an image depends on:
>   - **Resolution:** total number of pixels.  
>   - **Color depth:** number of bits used per pixel.

---

### Grayscale

**Grayscale images** use shades of gray, from black to white, with no color. Each pixel stores an intensity value, usually represented by 8 bits (0–255).

> [!info]
> - 0 = black  
> - 255 = white  
> - Values in between represent intermediate levels of gray.

> [!example]
> - Use case: medical imaging, old black-and-white photos, or simplifying image processing tasks.

---

### Indexed Color

In **indexed color** images, each pixel stores a numerical value (index) that points to a color in a predefined table (palette).

> [!example]
> - Example: If a pixel stores the value 3, the system retrieves the color at position 3 in the color table.

> [!important]
> - Advantage: reduces memory usage.  
> - Disadvantage: limited by the palette size. If the palette has 256 colors, no more can be displayed.

---

### Frame Buffer

A **frame buffer** is a section of memory that stores all the pixel data to be displayed. The screen refreshes from this memory multiple times per second.

> [!info]
> - How it works: the graphics card keeps a copy of the full image. During each refresh cycle, the monitor reads from it to draw the screen.

> [!note]
> - Importance: without a frame buffer, each pixel would need to be drawn manually in real time.

---

### Raster Graphics

**Raster graphics** are composed of pixels arranged in a rectangular grid (bitmap). They are excellent for representing detailed and realistic images.

> [!example]
> - Examples: photographs, scanned documents, screenshots.

> [!tip]
> - Pros: good for complex, high-detail visuals.  
> - Cons: resolution dependent. When zooming in, pixelation appears.

---

### Vector Graphics

**Vector graphics** use mathematical descriptions (lines, curves, and polygons) to define images. This makes them scalable without losing quality.

> [!example]
> - Examples: logos, fonts, CAD drawings.

> [!tip]
> - Pros: resolution independent, infinitely scalable.  
> - Cons: not suitable for realistic images like photos.

---

### Attributes of the Shapes

When you draw a shape (line, circle, polygon), it has several **visual attributes**:

- Color (fill and outline)  
- Line style (solid, dashed, dotted)  
- Thickness  
- Transparency  
- Shading or texture  

> [!note]
> These properties determine how geometric objects appear when rendered on screen.

---

### Display List

A **display list** is a stored sequence of drawing instructions that the GPU or graphics system can execute quickly.

> [!example]
> - Example: instead of recalculating the geometry of a cube every frame, store its instructions once and reuse them.

> [!important]
> - Benefit: saves computation time and increases rendering efficiency.

---

### Vacuum Tube Technology

Early displays, such as **Cathode Ray Tubes (CRTs)**, used vacuum tubes. An electron beam scanned across a phosphor-coated screen, producing light where it struck.

> [!note]
> - Historical note: this technology powered early televisions and computer monitors before LCDs and LEDs.

---

### Painting Programming

This approach is **pixel-based**, treating the image as a digital canvas where each pixel’s color can be modified directly.

> [!example]
> - Examples: MS Paint, Photoshop (when editing bitmaps).  
> - Focus: manipulating colors, brushes, and textures rather than geometric forms.

---

### Drawing Programming

This approach is **object-based**. Shapes are defined using mathematical descriptions (e.g., circles, rectangles, lines).

> [!example]
> - Examples: Adobe Illustrator, CAD software.  
> - Focus: geometric precision and scalable vector images.

---

### Lossless Data Compression

**Lossless compression** reduces file size without removing any information. Decompressed data is identical to the original image.

> [!example]
> - Examples: PNG, GIF, TIFF.  
> - Use case: images where accuracy is critical, such as medical or text-based images.

---

### Lossy Data Compression

**Lossy compression** removes some information to reduce file size. The result is visually similar but not identical to the original.

> [!example]
> - Examples: JPEG (images), MP3 (audio).  
> - Use case: photos and videos where small losses of detail are acceptable.

---

### Coordinate System of a Digital Image

Digital images use a 2D coordinate grid to identify each pixel. Two main conventions exist:

> [!info]
> - **Computer Graphics (OpenGL):** origin (0,0) at the bottom-left.  
> - **Image Processing (matrices, screen coordinates):** origin (0,0) at the top-left.

Each pixel is identified by its coordinates `(x, y)`:
- `x`: column position (horizontal).  
- `y`: row position (vertical).
