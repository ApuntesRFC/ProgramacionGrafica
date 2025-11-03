> [!summary]  
> **Matrix multiplication** combines rows of the left matrix with columns of the right matrix.  
> It’s defined **only if** the number of **columns of A = rows of B**, and it’s **not commutative** ($A·B \neq B·A$).

---

### 1. Conditions

For two matrices $A_{(m×n)}$ and $B_{(n×p)}$:

- Multiplication is **valid** only if  
    $$  
    \text{cols}(A) = \text{rows}(B)  
    $$
    
- The resulting matrix has size  
    $$  
    (m × p)  
    $$
    

---

### 2. Example: 2×2 Matrices

$$  
A =  
\begin{pmatrix}  
1 & 2 \\  
3 & 4  
\end{pmatrix},  
\quad  
B =  
\begin{pmatrix}  
5 & 6 \\  
7 & 8  
\end{pmatrix}  
$$

#### Then:

$$  
A·B =  
\begin{pmatrix}  
1·5 + 2·7 & 1·6 + 2·8 \  
3·5 + 4·7 & 3·6 + 4·8  
\end{pmatrix}

\begin{pmatrix}  
19 & 22 \  
43 & 50  
\end{pmatrix}  
$$

---

### 3. Visual Logic

Each **element** of the result is a **dot product** between:

- A **row** from the left matrix
    
- A **column** from the right matrix
    

Result element $C_{ij}$ = (row * column):

$$  
C_{ij} = \sum_{k=1}^{n} A_{ik} · B_{kj}  
$$

---

### 4. Example: 3×3 Matrices

# $$  
\begin{pmatrix}  
4 & 2 & 0 \  
0 & 8 & 1 \  
0 & 1 & 0  
\end{pmatrix}  
·  
\begin{pmatrix}  
4 & 2 & 1 \  
2 & 0 & 4 \  
9 & 4 & 2  
\end{pmatrix}

\begin{pmatrix}  
20 & 8 & 12 \  
25 & 4 & 34 \  
2 & 0 & 4  
\end{pmatrix}  
$$

---

> [!info]
> 
> - Matrix multiplication is **associative**: $(A·B)·C = A·(B·C)$
>     
> - It is **not commutative**: $A·B \neq B·A$
>     
> - Used in 3D graphics for chaining transformations (e.g., model × view × projection).
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat3 A, B, C;
>     C = A * B; // matrix multiplication
>     ```
>