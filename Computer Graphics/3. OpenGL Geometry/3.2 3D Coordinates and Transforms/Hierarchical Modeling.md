
> [!summary]
**Modeling transformations** allow each object to have its own coordinate system.  
In hierarchical modeling, we use a **transformation stack** to isolate transformations so that scaling, rotation, or translation applied to one object doesn’t affect others.

---

### Concept: Modeling Transformations and Hierarchical Modeling

Each object in a scene is defined in its **natural coordinate system** (local coordinates).  
To place it in the world or inside another object, we apply **modeling transformations**:

- **Scale** → set the object’s size.  
- **Rotate** → set its orientation.  
- **Translate** → move it into position.  

When combining multiple objects (like a windmill, robot arm, or car), each part has its own local coordinates and modeling transform.  
These are applied hierarchically: sub-objects inherit transformations from their parents.

---

### Why Use a Stack of Transforms

Without isolation, every new transform would accumulate and distort the rest of the scene.  
To avoid this, we use a **transformation stack**:

- **push()** → save the current transformation.  
- **pop()** → restore the last saved transformation.  

This ensures transformations only affect their intended object.

> [!info]
> Each time you draw a component:
> 1. Push the current transform.  
> 2. Apply transformations specific to that component.  
> 3. Draw it.  
> 4. Pop to restore the previous state.

---

### OpenGL’s Classic Matrix Stack (Legacy)

Older OpenGL (before 3.2 Core) provided built-in matrix stacks:

```c
glMatrixMode(GL_MODELVIEW); // select model-view stack
glPushMatrix();             // save current matrix
glTranslatef(dx, dy, dz);   // apply transformations
glRotatef(angle, ax, ay, az);
drawCube();
glPopMatrix();              // restore matrix
````

Each push saves the current **composed transformation matrix**;  
each pop restores the previous one.

> [!note]  
> This system is no longer available in modern OpenGL (Core Profile).  
> You now manage your own **matrix stack** on the CPU (e.g., using `std::stack<glm::mat4>` or your own implementation).

---

### Modern Equivalent (GLM)

```cpp
std::stack<glm::mat4> matrixStack;
glm::mat4 current = glm::mat4(1.0f); // identity

// draw main object
matrixStack.push(current);
current = glm::translate(current, {0, 0, -2});
current = glm::rotate(current, glm::radians(45.0f), {0, 1, 0});
drawModel(current);
current = matrixStack.top(); matrixStack.pop();

// draw subobject (independent)
matrixStack.push(current);
current = glm::translate(current, {2, 0, 0});
current = glm::scale(current, {0.5f, 0.5f, 0.5f});
drawModel(current);
current = matrixStack.top(); matrixStack.pop();
```

> [!tip]  
> Always push before transforming a part, and pop after drawing it — this keeps transforms localized.

---

### Example: Coloring Cube Faces (Conceptual)

To draw a cube where each face is a different color:

1. Push matrix before drawing a face.
    
2. Apply a translation/rotation to position the face.
    
3. Set its color.
    
4. Draw the square.
    
5. Pop to restore.
    

> [!example]
> 
> ```c
> glPushMatrix();
>   glColor3f(1,0,0);       // red
>   glTranslatef(0, 0, 1);  // move to front face
>   drawSquare();
> glPopMatrix();
> 
> glPushMatrix();
>   glColor3f(0,1,0);       // green
>   glTranslatef(0, 0, -1); // back face
>   drawSquare();
> glPopMatrix();
> ```
> 
> Each push/pop isolates the transform for that face.

---

### Key Takeaways

- Modeling transformations define _where_ and _how_ an object appears.
    
- The transformation stack isolates each object’s local transformations.
    
- In **modern OpenGL**, implement your own stack and pass matrices as uniforms.
    
- Legacy functions (`glPushMatrix`, `glPopMatrix`, etc.) are deprecated but conceptually identical.
    

> [!important]  
> Hierarchical modeling depends on proper use of the transformation stack — this is the foundation for building complex animated structures like robotic arms, vehicles, or articulated models.
