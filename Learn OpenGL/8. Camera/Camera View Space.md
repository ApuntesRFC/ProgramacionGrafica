> [!summary]  
> **Camera/View space** is the world seen from the camera's point of view.  
> The **view matrix** transforms world coordinates into coordinates relative to the camera's position and orientation.

---

### 1. Concept

- When talking about **camera space** (or **view space**), we're referring to all vertex coordinates as seen from the **camera's perspective as the origin**.
    
- The **view matrix** transforms world coordinates into view coordinates that are relative to the camera's position and direction.
    
- To define a camera, we need:
    
    - Its **position** in world space.
        
    - The **direction** it's looking at.
        
    - A vector pointing to the **right**.
        
    - A vector pointing **upwards** from the camera.
        

---

### 2. Camera Coordinate System

We're essentially creating a **coordinate system with 3 perpendicular unit axes**:

- **Right** → camera's $+x$ axis
    
- **Up** → camera's $+y$ axis
    
- **Direction/Forward** → camera's $+z$ axis
    

With the **camera's position as the origin**, these vectors form the basis for the **view matrix**.

---

> [!info]
> 
> - Building these three perpendicular axes is essentially a **Gram-Schmidt process** from linear algebra.
>     
> - Once we have the camera's basis vectors and position, we can create a **LookAt matrix** to transform the world into camera space.