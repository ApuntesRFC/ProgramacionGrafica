> [!summary]  
> **Camera abstraction = manage $V$ and $P$ cleanly.** Position/orient the camera (build $V=C^{-1}$) and define the view volume (build $P$). En OpenGL 3.3+ core no uses `glMatrixMode`, GLU ni utilidades legacy; usa GLM u otra librería y pasa matrices por **uniforms**.

---

### Concept: Camera = Pose + Projection

- **Pose (camera in world):** posición $\mathbf{c}$, dirección frontal $\mathbf{f}$, arriba $\mathbf{u}$, derecha $\mathbf{r}=\mathbf{f}\times\mathbf{u}$.
    
- **View matrix:** mueve el mundo al marco de cámara.  
    $$  
    V=\text{lookAt}(\mathbf{c},,\mathbf{c}+\mathbf{f},,\mathbf{u})=C^{-1}  
    $$
    
- **Projection:** define la **view volume** y mapea **Eye → Clip**.
    
    - **Perspective:** $$P=\text{perspective}(\text{fov}_y,\ a,\ z_\text{near}, z_\text{far})$$
        
    - **Orthographic:** $$P=\text{ortho}(l,r,b,t, z_\text{near}, z_\text{far})$$
        

> [!info]  
> Near y far deben ser **positivos** en perspectiva y $z_\text{far}!>!z_\text{near}$. En ortográfica $z_\text{near}$ puede ser negativo. Ajusta **aspect** $a=\tfrac{w}{h}$ al **viewport**.

---

### Modern API usage (GLFW/GLAD + GLM)

```cpp
// View (camera)
glm::mat4 V = glm::lookAt(camPos, camPos + camDir, camUp);

// Projection (recalcula si cambia el tamaño del framebuffer)
glm::mat4 P = glm::perspective(glm::radians(fovDeg), aspect, zNear, zFar);

// Upload
glUseProgram(program);
glUniformMatrix4fv(glGetUniformLocation(program,"uView"), 1, GL_FALSE, glm::value_ptr(V));
glUniformMatrix4fv(glGetUniformLocation(program,"uProj"), 1, GL_FALSE, glm::value_ptr(P));
```

> [!warning]  
> No uses `glMatrixMode`, `glLoadIdentity`, `glFrustum`, `glOrtho`, `gluPerspective`, `gluLookAt` en **core profile**. Gestiona tus matrices en CPU.

---

### Camera controls comunes

#### Orbit/Trackball (rotar alrededor de un punto de interés $\mathbf{t}$)

- Parámetros: distancia $d$, ángulos $(\theta,\phi)$.
    
- Posición:  
    $$  
    \mathbf{c}=\mathbf{t}+\begin{bmatrix}  
    d\cos\phi\sin\theta\  
    d\sin\phi\  
    d\cos\phi\cos\theta  
    \end{bmatrix}  
    ,\quad \mathbf{f}=\frac{\mathbf{t}-\mathbf{c}}{\lVert \mathbf{t}-\mathbf{c}\rVert}  
    $$
    
- Wheel: ajusta $d$. Drag: ajusta $(\theta,\phi)$. Shift+drag: **pan** sobre el plano de la cámara.
    

#### FPS (free look)

- Orientación por yaw/pitch.  
    $$  
    \mathbf{f}=  
    \begin{bmatrix}  
    \cos!\text{pitch}\ \cos!\text{yaw}\  
    \sin!\text{pitch}\  
    \cos!\text{pitch}\ \sin!\text{yaw}  
    \end{bmatrix}  
    $$
    
- WASD desplaza $\mathbf{c}$ en el plano $(\mathbf{r},\mathbf{f}_\text{planar})$.
    

> [!tip]  
> Mantén $\mathbf{u}$ ortonormal: re-ortogonaliza $\mathbf{r}=\text{normalize}(\mathbf{f}\times\mathbf{u}_\text{world})$, $\mathbf{u}=\text{normalize}(\mathbf{r}\times\mathbf{f})$.

---

### Resize y aspecto

- En callback de **framebuffer size**:
    
    ```cpp
    void framebuffer_size_callback(GLFWwindow*, int w, int h){
        glViewport(0,0,w,h);
        aspect = (h>0) ? float(w)/float(h) : 1.0f;
        P = glm::perspective(glm::radians(fovDeg), aspect, zNear, zFar);
    }
    ```
    

> [!note]  
> La viewport **no** recorta por sí misma. Para recorte duro usa **scissor**: `glEnable(GL_SCISSOR_TEST); glScissor(x,y,w,h);`.

---

### Depth y estabilidad

- OpenGL mapea $$z_\text{ndc}\in[-1,1]\ \to\ z_\text{win}=\tfrac{z_\text{ndc}+1}{2}\in[0,1]$$
    
- Precisión de profundidad no lineal en perspectiva. Minimiza $z_\text{far}/z_\text{near}$ para reducir **z-fighting**.
    

> [!tip]  
> Empieza con $z_\text{near}\approx 0.1$–$0.5$, $z_\text{far}$ lo más cercano posible al escenario real. Ajusta con métricas.

---

### M–V–P integración

Pipeline por objeto:  
$$  
\mathbf{p}_\text{clip}=P,V,M,\mathbf{p}_\text{obj},\quad  
\mathbf{p}_\text{ndc}=\frac{\mathbf{p}_\text{clip}}{w}  
$$

```glsl
#version 330 core
layout(location=0) in vec3 aPos;
uniform mat4 uModel, uView, uProj;
void main(){
    gl_Position = uProj * uView * uModel * vec4(aPos, 1.0);
}
```

---

### Reemplazo de utilidades legacy

> [!important]  
> Si tu material menciona:
> 
> - `cameraApply`, `cameraSetLimits`, `cameraSetOrthographic`, `cameraSetPreserveAspect`  
>     **Traducción moderna:** mantén una **clase Camera** propia con:
>     
> 
> 1. estado: `camPos`, `camDir`, `camUp`, `fov`, `aspect`, `zNear`, `zFar`, `target` (opcional).
>     
> 2. métodos: `updateView()`, `updateProjection(w,h)`, `orbit(dx,dy)`, `pan(dx,dy)`, `zoom(d)`.
>     
> 3. salida: matrices `V` y `P` como **uniforms**.
>     

---

### Checklist de cámara

- Define **pose** y construye $$V=\text{lookAt}$$.
    
- Define **proyección** acorde a uso y **aspect**.
    
- Actualiza **viewport** y $P$ en cada resize.
    
- Implementa **controles** estables (orbit o FPS).
    
- Mantén **near/far** ajustados y verifica el **depth test**.