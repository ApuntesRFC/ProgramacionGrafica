> [!summary]  
> A **matrix-scalar product** multiplies every element of a matrix by a **single scalar value**, effectively **scaling** the entire matrix.

---

### 1. Definition

Given a scalar $s$ and a matrix:

$$  
A =  
\begin{pmatrix}  
1 & 2 \\  
3 & 4  
\end{pmatrix}  
$$

then:

$$  
sA =  
\begin{pmatrix}  
s \cdot 1 & s \cdot 2 \  
s \cdot 3 & s \cdot 4  
\end{pmatrix}  
$$

---

### 2. Example

If $s = 2$, then:

 $$  
2 \cdot  
\begin{pmatrix}  
1 & 2 \\  
3 & 4  
\end{pmatrix}

\begin{pmatrix}  
2 & 4 \\  
6 & 8  
\end{pmatrix}  
$$

---

> [!info]
> 
> - The scalar **scales all matrix elements** proportionally.
>     
> - A scalar value of **1** leaves the matrix unchanged.
>     
> - A scalar value of **0** produces the **zero matrix**.
>     
> - In graphics, scalar multiplication is useful for **uniform scaling** (e.g., enlarging an object by a factor).
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat2 A(1,2,3,4);
>     glm::mat2 B = 2.0f * A; // scales every element by 2
>     ```
>