
> [!summary]
In 2D and 3D graphics, coordinates are **numerical representations** used to describe the position of real points and objects in space.  
Mathematical transforms let us manipulate these coordinates to move, rotate, or scale objects in a physically meaningful way.

---

### 2D Coordinates

A **2D point** requires two values:
$$
(x, y)
$$
These numbers represent the position of a point in a plane.

> [!note]
> - Points and shapes are **real geometric entities**.  
> - Coordinates are **abstract numbers** assigned to them for reference and computation.  
> - Transformations (translation, rotation, scaling) are **mathematical operations** with clear geometric effects.

---

### 3D Coordinates

In **3D space**, each point is defined by three values:
$$
(x, y, z)
$$
The third coordinate, $z$, adds depth — perpendicular to both $x$ and $y$.

> [!info]
> In typical visualization:
> - **x-axis** → green  
> - **y-axis** → blue  
> - **z-axis** → red  

[Interactive example — 3D Axes Visualization](https://math.hws.edu/eck/cs424/graphicsbook-1.4/demos/c3/axes3D.html)

---

### From 3D to 2D: Projection

Objects that are farther away **appear smaller** when projected onto a 2D image.  
This effect results from **perspective projection**, where the 3D world is mapped onto the 2D viewing plane.

> [!note]
> Shading enhances this illusion by simulating how light interacts with surfaces, giving a sense of depth and form.

---

### Axis Orientation and Handedness

> [!info]
> - In most conventions, $x$ and $y$ lie in the screen plane.  
> - $z$ increases **toward or into** the screen, depending on the coordinate system.

OpenGL uses a **right-handed coordinate system** by default for 3D math (via GLM and modern APIs),  
but the **clip space** after projection is **left-handed** in normalized device coordinates (NDC).

> [!tip]
> To visualize right-handed orientation:  
> point your **right thumb** along +Z; your fingers curl from +X to +Y.

---

### Coordinate Systems Overview

> [!info]
> In graphics, several coordinate systems are used at different stages of rendering:

| Coordinate System | Description | Example |
|--------------------|-------------|----------|
| **Object coordinates** | Local coordinates used to define the object’s geometry. | Vertices defined in model space. |
| **World coordinates** | After applying **modeling transforms** to place the object in the scene. | Position of all objects in the world. |
| **Eye/View coordinates** | From the **camera’s point of view** (viewer space). | What the viewer “sees.” |
| **Clip/Screen coordinates** | After projection and viewport transforms, ready for rasterization. | Final 2D screen positions. |

> [!note]
> Each stage applies a transformation to move from one space to the next:
> $$
> \text{Object} \rightarrow \text{World} \rightarrow \text{View} \rightarrow \text{Clip} \rightarrow \text{Screen}
> $$

---

### Transformations and Their Role

Every vertex in a 3D scene passes through a **sequence of transformations** before it is rendered:
1. **Model transform:** positions an object within the world.  
2. **View transform:** converts world coordinates into the camera’s perspective.  
3. **Projection transform:** maps 3D coordinates to 2D space.  
4. **Viewport transform:** scales to pixel coordinates.

> [!tip]
> Understanding how these coordinate systems and transformations interact is key to mastering modern OpenGL and 3D rendering.
