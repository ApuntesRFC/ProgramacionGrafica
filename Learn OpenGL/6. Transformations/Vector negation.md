> [!summary]  
> **Negating a vector** reverses its direction while keeping its magnitude unchanged.  
> The vector simply points in the **exact opposite** direction.

---

### Formula

Given  
$$  
\vec{v} =  
\begin{pmatrix}  
v_x \\ v_y \\ v_z  
\end{pmatrix}  
$$

its negation is:

$$  
-\vec{v} =  
\begin{pmatrix}

- v_x \\ - v_y \\ - v_z  
    \end{pmatrix}  
    $$
    

or equivalently,

$$  
-\vec{v} = (-1) \cdot \vec{v}  
$$

---

### Example

If  
$$  
\vec{v} = (2, 3, 0)  
$$  
then  
$$  
-\vec{v} = (-2,-3,0)  
$$

So a vector pointing **northeast** now points **southwest**, but both have the same length.

---

### Information Addendum
> [!info]
> - Negation only changes **direction**, never **length**.
>     
> - Useful for computing reflection, inverse movement, or opposite forces.
>     
> - In graphics programming (e.g., GLM):
>     
>     ```cpp
>     glm::vec3 v(2.0f, 3.0f, 0.0f);
>     glm::vec3 neg = -v; // (-2, -3, 0)
>     ```
>


