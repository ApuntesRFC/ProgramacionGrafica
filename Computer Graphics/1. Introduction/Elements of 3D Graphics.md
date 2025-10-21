> [!summary]
Geometric modeling and 3D graphics fundamentals: object construction, transformations, lighting, textures, projections, and the full rendering pipeline.

---

### Geometric Modeling

**Geometric modeling** is the process of creating mathematical representations of shapes and objects. In computer graphics, it defines the structure of 2D and 3D objects before rendering.

> [!example]
> - Example: a cube can be modeled by defining its vertices and connecting them with edges and faces.

> [!note]
> - Importance: it is the foundation for rendering, animation, and simulation.

Models can be built using equations, meshes, or geometric primitives.

---

### Geometry of 3D Graphics

The **geometry of 3D graphics** provides the mathematical framework for describing shapes in three-dimensional space. Objects are defined through points, lines, planes, and surfaces within a coordinate system.

> [!example]
> - A point is defined by `(x, y, z)`.  
> - A vector has direction and magnitude.  
> - A surface can be expressed through parametric equations.

> [!tip]
> - Geometry defines the structure; rendering later provides the appearance.

---

### World Coordinates

**World coordinates** are the global reference frame in a 3D scene. Local object coordinates are transformed into this system to ensure consistency among all models.

> [!example]
> - Example: a tree’s trunk might be modeled around its own origin `(0,0,0)` but placed at world position `(30,0,10)`.

> [!note]
> - Purpose: unify all models within one coherent scene.

---

### Geometric Primitives

**Geometric primitives** are the simplest elements used to construct more complex models.

> [!info]
> - 2D primitives: points, lines, circles, polygons.  
> - 3D primitives: cubes, spheres, cylinders, cones.

Complex models are combinations or hierarchies of these primitives.

---

### Hierarchical Modeling

**Hierarchical modeling** organizes scene objects using parent–child relationships. Transformations applied to a parent affect all its children.

> [!example]
> - Example: in a human model, the arm is attached to the torso, and the hand to the arm. Rotating the arm moves the hand as well.

> [!important]
> - Benefit: simplifies manipulation and animation of complex structures.

---

### Geometric Transform

**Geometric transformations** modify object position, orientation, or scale using matrix operations in 2D or 3D.

> [!info]
> - Main types:
>   - Translation (move)
>   - Rotation (turn)
>   - Scaling (resize)
>   - Reflection (mirror)
>   - Shear (skew)

---

### Scaling, Rotation, and Translation

> [!example]
> - **Scaling**: changes object size (uniform or non-uniform). Example: doubling a cube’s dimensions.  
> - **Rotation**: turns an object around an axis (x, y, or z). Example: rotating a wheel around its center.  
> - **Translation**: moves an object in space. Example: shifting a chair across the room.

These transformations are combined through matrix multiplication for precise spatial control.

---

### Appearance of 3D Graphics

Once objects are modeled, they require **appearance attributes** such as material, color, reflectivity, transparency, and texture to define how they interact with light and achieve realism.

---

### Materials

**Materials** describe how surfaces reflect or absorb light.  

> [!example]
> - Metal reflects strongly.  
> - Glass transmits light (transparent).  
> - Rubber is matte.

> [!info]
> - Common parameters:
>   - Ambient color  
>   - Diffuse reflection  
>   - Specular highlights  
>   - Shininess factor

---

### Texture

**Textures** are 2D images mapped onto 3D surfaces to add visual detail without adding geometry.

> [!example]
> - Example: a photo of bricks mapped onto a flat wall polygon.

> [!important]
> - Benefit: achieves visual richness with lower computational cost.

---

### Lighting

**Lighting** simulates how light interacts with surfaces to create realism through brightness, shadows, and highlights.

> [!info]
> - Types of light:
>   - Ambient (general illumination)
>   - Diffuse (scattered reflection)
>   - Specular (direct, shiny highlights)

> [!note]
> - Common lighting models: **Phong** and **Blinn–Phong**.

---

### Images of 3D Graphics

The **image** is the final output after modeling, transformation, and rendering.  
It represents the projection of a 3D world onto a 2D screen.

---

### Viewing

**Viewing** defines what part of the 3D scene is visible and how it is observed. It involves setting the **camera position**, **orientation**, and **view volume**.

> [!example]
> - Analogy: placing a virtual camera in the 3D world to capture the scene.

---

### Projection

**Projection** converts 3D coordinates into 2D screen coordinates.

> [!info]
> - **Orthographic projection:** parallel lines remain parallel, no depth distortion.  
> - **Perspective projection:** objects farther away appear smaller, simulating human vision.

---

### Rasterization

**Rasterization** converts geometric primitives (lines, triangles, polygons) into discrete pixels. It determines which pixels belong to each shape and assigns color values.

> [!note]
> - Rasterization bridges geometry and image representation.

---

### Rendering

**Rendering** is the full process of generating a 2D image from a 3D model. It integrates geometry, lighting, shading, rasterization, and texturing.

> [!info]
> - Types:
>   - **Real-time rendering:** used in video games.  
>   - **Offline rendering:** used in films and high-quality visuals.

---

### Animation

**Animation** creates motion by displaying a sequence of frames where objects change position, orientation, or appearance over time.

> [!example]
> - Example: moving a character step by step across a scene.

> [!tip]
> - Main methods:
>   - Keyframe animation  
>   - Motion capture  
>   - Procedural animation
