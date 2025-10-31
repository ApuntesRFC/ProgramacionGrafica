> [!summary]  
> A **scalar** is a single numeric value. When combined with a **vector**, the operation applies to **each component individually**.

---

### Scalar–Vector operations

Let  
$$  
\vec{v} =  
\begin{pmatrix}  
1 \ 2 \ 3  
\end{pmatrix},  
\quad  
x \in \mathbb{R}  
$$

Then:

#### Addition / Subtraction

$$  
\vec{v} + x =  
\begin{pmatrix}  
1 + x \ 2 + x \ 3 + x  
\end{pmatrix},  
\qquad  
\vec{v} - x =  
\begin{pmatrix}  
1 - x \ 2 - x \ 3 - x  
\end{pmatrix}  
$$

#### Multiplication

$$  
\vec{v} \cdot x =  
\begin{pmatrix}  
1 \cdot x \ 2 \cdot x \ 3 \cdot x  
\end{pmatrix}  
$$

#### Division

$$  
\vec{v} / x =  
\begin{pmatrix}  
1/x \ 2/x \ 3/x  
\end{pmatrix}  
$$

---

### Key idea

> Each component of the vector is independently modified by the scalar.  
> The operation acts _component-wise_:
> 
> $$  
> \vec{v}_{new}[i] = f(\vec{v}[i], x)  
> $$

---

### Information Addendum
>[!info]
>- The scalar affects **magnitude**, not direction (except for negative multiplication, which reverses it).
>     
> - In computer graphics, scaling transformations often use **scalar–vector multiplication**.
>     
> - Example: doubling an object’s size → multiply its position vectors by `2.0f`.
>
