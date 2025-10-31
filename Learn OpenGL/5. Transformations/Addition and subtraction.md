> [!summary]  
> **Vector addition** combines two directions component by component.  
> **Vector subtraction** gives the difference (the direction and distance from one point to another).

---

### 1. Vector Addition

Given:  
$$  
\vec{v} =  
\begin{pmatrix}  
1 \ 2 \ 3  
\end{pmatrix},  
\quad  
\vec{k} =  
\begin{pmatrix}  
4 \ 5 \ 6  
\end{pmatrix}  
$$

Then:  
$$  
\vec{v} + \vec{k} =  
\begin{pmatrix}  
1 + 4 \  
2 + 5 \  
3 + 6  
\end{pmatrix} =  
\begin{pmatrix}  
5 \ 7 \ 9  
\end{pmatrix}  
$$

> [!example]  
> In 2D, if $\vec{v} = (4, 2)$ and $\vec{k} = (1, 2)$,  
> the **head-to-tail method** means you place $\vec{k}$’s tail at $\vec{v}$’s head.  
> The result goes from $\vec{v}$’s start to $\vec{k}$’s end.

---

### 2. Vector Subtraction

Subtracting one vector from another:  
$$  
\vec{v} - \vec{k} =  
\begin{pmatrix}  
1 - 4 \  
2 - 5 \  
3 - 6  
\end{pmatrix} =  
\begin{pmatrix}  
-3 \ -3 \ -3  
\end{pmatrix}  
$$

This is equivalent to:  
$$  
\vec{v} + (-\vec{k})  
$$

> [!info]  
> Subtraction gives a vector **from** the tip of $\vec{k}$ **to** the tip of $\vec{v}$.  
> Useful for calculating _direction vectors_ or _displacement_ between points.

---

### Information Addendum
> [!info]
> - **Addition →** combines two displacements.
>     
> - **Subtraction →** measures the change or offset between positions.
>     
> - In graphics, `vecB - vecA` gives the **direction from A to B**.
>     
> - In GLM:
>     
>     ```cpp
>     glm::vec3 v(1, 2, 3);
>     glm::vec3 k(4, 5, 6);
>     glm::vec3 sum = v + k;     // (5, 7, 9)
>     glm::vec3 diff = v - k;    // (-3, -3, -3)
>     ```
>


