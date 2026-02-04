> [!summary]  
> The **camera position** is a vector in **world space** that specifies where the camera is located.

---

### Getting the Camera Position

Defining the camera position is straightforward. We simply create a vector in world space:

```cpp
glm::vec3 cameraPos = glm::vec3(0.0f, 0.0f, 3.0f);
```

This sets the camera at the same position we used in the previous chapter.

---

> [!info]
> 
> - In OpenGL, the **positive $z$-axis** goes **through your screen towards you**.
>     
> - If we want the camera to move **backwards**, we move along the **positive $z$-axis**.
>     
> - This is the first step in building the camera's coordinate system.