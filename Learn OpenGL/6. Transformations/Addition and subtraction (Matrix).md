> [!summary]  
> **Matrix addition and subtraction** operate **element by element** and are **only defined** when both matrices have **identical dimensions**.

---

### 1. Matrix Addition

Given two matrices of equal size:

$$  
A =  
\begin{pmatrix}  
1 & 2 \  
3 & 4  
\end{pmatrix},  
\quad  
B =  
\begin{pmatrix}  
5 & 6 \  
7 & 8  
\end{pmatrix}  
$$

their sum is:

# $$  
A + B =  
\begin{pmatrix}  
1+5 & 2+6 \  
3+7 & 4+8  
\end{pmatrix}

\begin{pmatrix}  
6 & 8 \  
10 & 12  
\end{pmatrix}  
$$

---

### 2. Matrix Subtraction

Similarly, subtraction is performed element-wise:

$$  
A =  
\begin{pmatrix}  
4 & 2 \  
1 & 6  
\end{pmatrix},  
\quad  
B =  
\begin{pmatrix}  
2 & 4 \  
0 & 1  
\end{pmatrix}  
$$

then:

# $$  
A - B =  
\begin{pmatrix}  
4-2 & 2-4 \  
1-0 & 6-1  
\end{pmatrix}

\begin{pmatrix}  
2 & -2 \  
1 & 5  
\end{pmatrix}  
$$

---

> [!info]
> 
> - Matrix **addition** and **subtraction** are **commutative** and **associative**:  
>     $A + B = B + A$, $(A + B) + C = A + (B + C)$
>     
> - They are **undefined** for matrices of different sizes.
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat2 A(1,2,3,4);
>     glm::mat2 B(5,6,7,8);
>     glm::mat2 C = A + B; // element-wise
>     ```
>