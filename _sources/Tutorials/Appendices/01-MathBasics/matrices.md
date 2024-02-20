# Matrices

Matrices are fundamental mathematical structures that play a crucial role in various fields, including mathematics, physics, engineering, and computer graphics as well. They provides a compact and efficient way to express transformations, such as scaling, rotation, and translation. This feature enables easy manipulation of the position, orientation, or size of 3D objects in a rendering scene in an easy way.

<br>

## Definition

Basically, a matrix is just a rectangular table of real numbers arranged in rows and columns.<br>
The dimensions of a matrix are determined by the number of its rows and columns. For instance, an $m\times n$ matrix consists of $m$ rows and $n$ columns. The individual numbers within a matrix are referred to as elements or entries. To indicate a specific entry within a matrix $\mathbf{M}$, a double subscript notation, such as $M_{ij}$, is often used to specify the element located in the i-th row and j-th column. Matrices are conventionally denoted using square brackets.

$$\mathbf{M}=\left\lbrack\matrix{M_{00}&M_{01}&\cdots&M_{0(n-1)} \cr M_{10}&M_{11}&\cdots&M_{1(n-1)} \cr \vdots&\vdots&\ddots&\vdots \cr M_{(m-1)1}&\cdots&M_{(m-1)0}&M_{0(n-1)}}\right\rbrack$$

In computer graphics, it is common to work with square matrices $n\times n$, where the number of rows and columns are equal. Row and column vectors, which are matrices with dimensions of $1\times n$ and $n\times 1$ respectively, are also widely used. When it comes to row and column vectors, a different naming convention is typically used: lowercase letters are used instead of uppercase ones. This is because row and column vectors can be used as operands in certain vector operations.

$$\mathbf{u}=\left\lbrack\matrix{u_0&u_1&\cdots&u_{n-1}}\right\rbrack\quad\quad\quad\quad\mathbf{v}=\left\lbrack\matrix{v_0 \cr v_1 \cr \vdots \cr v_{n-1}}\right\rbrack$$

We can also use them to specify the rows or columns of a matrix. For example, we can write the following $3\times 3$ matrix $\mathbf{M}$ by using 3 row vectors, or 3 column vectors.

$$\mathbf{M}=\left\lbrack\matrix{M_{00}&M_{01}&M_{02}\cr M_{10}&M_{11}&M_{12}\cr M_{20}&M_{21}&M_{22}}\right\rbrack=\left\lbrack\matrix{\gets&\mathbf{u}_0&\rightarrow\cr \gets&\mathbf{u}_1&\rightarrow\cr \gets&\mathbf{u}_2&\rightarrow}\right\rbrack=\left\lbrack\matrix{\uparrow&\uparrow&\uparrow\cr \mathbf{v}_0&\mathbf{v}_1&\mathbf{v}_2\cr \downarrow&\downarrow&\downarrow}\right\rbrack$$

where $\mathbf{u_i}=\left\lbrack\matrix{M_{i0}&M_{i1}&M_{i2}}\right\rbrack$, $\ \mathbf{v_i}=\left\lbrack\matrix{M_{0i}&M_{1i}&M_{2i}}\right\rbrack$ and $0\le i<3$.

<br>

## Basic matrix operations 

There are several interesting operations that can be performed with matrices, including addition, subtraction, and two different types of multiplication.


### Addition

The addition of two matrices $\mathbf{A}$ and $\mathbf{B}$ can only be performed if the matrices have the same dimensions. If this condition is met, the corresponding elements of the matrices are added together.

$$\mathbf{A}+\mathbf{B}=\left\lbrack\matrix{A_{00}&A_{01}&A_{02}\cr A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}}\right\rbrack+\left\lbrack\matrix{B_{00}&B_{01}&B_{02}\cr B_{10}&B_{11}&B_{12}\cr B_{20}&B_{21}&B_{22}}\right\rbrack=\left\lbrack\matrix{A_{00}+B_{00}&A_{01}+B_{01}&A_{02}+B_{02}\cr A_{10}+B_{10}&A_{11}+B_{11}&A_{12}+B_{12}\cr A_{20}+B_{20}&A_{21}+B_{21}&A_{22}+B_{22}}\right\rbrack$$

The difference of two matrices is defined in a similar way.

$$\mathbf{A}-\mathbf{B}=\left\lbrack\matrix{A_{00}&A_{01}&A_{02}\cr A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}}\right\rbrack-\left\lbrack\matrix{B_{00}&B_{01}&B_{02}\cr B_{10}&B_{11}&B_{12}\cr B_{20}&B_{21}&B_{22}}\right\rbrack=\left\lbrack\matrix{A_{00}-B_{00}&A_{01}-B_{01}&A_{02}-B_{02}\cr A_{10}-B_{10}&A_{11}-B_{11}&A_{12}-B_{12}\cr A_{20}-B_{20}&A_{21}-B_{21}&A_{22}-B_{22}}\right\rbrack$$


### Scalar multiplication 

We can multiply a matrix $\mathbf{M}$ with a scalar $k$ by scaling all the elements of the matrix by $k$.

$$k\mathbf{M}=\left\lbrack\matrix{kM_{00}&kM_{01}&kM_{02}\cr kM_{10}&kM_{11}&kM_{12}\cr kM_{20}&kM_{21}&kM_{22}}\right\rbrack$$


#### Properties of addition and scalar multiplications

Since addition and scalar multiplication are performed element-wise on matrices, they inherit the following properties from real numbers:

```{list-table}
:header-rows: 1

* - Name
  - Property
* - Commutative
  - $\mathbf{A}+\mathbf{B} = \mathbf{B}+\mathbf{A}$
* - Associative
  - $(\mathbf{A}+\mathbf{B})+\mathbf{C}=\mathbf{A}+(\mathbf{B}+\mathbf{C})$
* - Distributive (scalar)
  - $(k+t)\mathbf{A} = k\mathbf{A}+t\mathbf{A}$
* - Distributive (matrix)
  - $k(\mathbf{A}+\mathbf{B}) = k\mathbf{A}+k\mathbf{B}$
```

### Matrix multiplication

In order to multiply two matrices $\mathbf{A}$ and $\mathbf{B}$, it is necessary for the number of columns in $\mathbf{A}$ to be equal to the number of rows in $\mathbf{B}$. This requirement arises because each element $C_{ij}$ of the resulting matrix $\mathbf{C}$ is obtained by taking the dot product of the i-th row of $\mathbf{A}$ and the j-th column of $\mathbf{B}$. This means that, if $\mathbf{A}$ is an $m\times n$ matrix and $\mathbf{B}$ is an $n\times p$ matrix, the resulting matrix $\mathbf{C}$ will have dimensions $m\times p$. This is because we need to multiply each of the $m$ rows of $\mathbf{A}$ by each of the $p$ columns of $\mathbf{B}$ in order to obtain all the elements of $\mathbf{C}$. That is,

$$\begin{equation}\tag{1}C_{ij}=\mathbf{A}_{i\ast}\cdot\mathbf{B}_{\ast j}\label{eq:AMatrices1}\end{equation}$$

where $\mathbf{A}_{i\ast}$ is the i-th row of $\mathbf{A}$, and $\mathbf{B}_{\ast j}$ is the j-th column of $\mathbf{B}$.

As an example, the following illustration shows that if we multiply a $2\times 3$ matrix $\mathbf{A}$ by a $3\times 2$ matrix $\mathbf{B}$, we get a $2\times 2$ matrix $\mathbf{C}$ where the top-left element $C_{00}$ is computed by taking the dot product of the 0-th row of $\mathbf{A}$ and the 0-th column of $\mathbf{B}$.

```{figure} images/02/matrix-multiplication.png
```

Observe that matrix multiplication is always defined for square matrices of the same dimension, and the resulting matrix will have the same dimensions as the operands.


#### Vector-Matrix multiplication

A row vector is a $1\times n$ matrix, so we can multiply it with a generic $n\times p$ matrix. For example, if $\mathbf{u}=(x, y, z)$ is a three-component vector that represent a row vector and $\mathbf{A}$ is a $3\times 3$ matrix, applying equation $\eqref{eq:AMatrices1}$ we have that

$$\mathbf{uA}=\left\lbrack\matrix{x&y&z}\right\rbrack\left\lbrack\matrix{A_{00}&A_{01}&A_{02}\cr A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}}\right\rbrack=\left\lbrack\matrix{\mathbf{u}\cdot\mathbf{A}_{\ast 0}&\mathbf{u}\cdot\mathbf{A}_{\ast 1}&\mathbf{u}\cdot\mathbf{A}_{\ast 2}}\right\rbrack$$

The result is a row vector with three elements (just like $\mathbf{u}$) where each entry is the dot product of $\mathbf{u}$ and a column of $\mathbf{A}$. Now, if we actually perform the dot product we have

$$
\begin{align*}
\mathbf{uA}&=\left\lbrack\matrix{\mathbf{u}\cdot\mathbf{A}_{\ast 0}&\mathbf{u}\cdot\mathbf{A}_{\ast 1}&\mathbf{u}\cdot\mathbf{A}_{\ast 2}}\right\rbrack \\
&=\left\lbrack\matrix{xA_{00}+yA_{10}+zA_{20}&\quad xA_{01}+yA_{11}+zA_{21}&\quad xA_{02}+yA_{12}+zA_{22}}\right\rbrack \\
&=\left\lbrack\matrix{xA_{00}&xA_{01}&xA_{02}}\right\rbrack+\left\lbrack\matrix{yA_{10}&yA_{11}&yA_{12}}\right\rbrack+\left\lbrack\matrix{zA_{20}&zA_{21}&zA_{22}}\right\rbrack \\
&=x\mathbf{A}_{0\ast}+y\mathbf{A}_{1\ast}+z\mathbf{A}_{2\ast}
\end{align*}
$$

So, the vector-matrix multiplication can be seen as a sum of the rows of the matrix, scaled by the elements of the vector. This is an example of linear combination: a sum of vectors multiplied (scaled) by scalar coefficients. In this case, the vectors are the row vectors of the matrix, while the scalar coefficients are the elements of the vector acting as left operandin the multiplication. We can also write this linear combination as the product of a row vector and a column vector.

$$\mathbf{uA}=x\mathbf{A}_{0\ast}+y\mathbf{A}_{1\ast}+z\mathbf{A}_{2\ast}=\left\lbrack\matrix{x&y&z}\right\rbrack\left\lbrack\matrix{\mathbf{A}_{0\ast}\cr \mathbf{A}_{1\ast}\cr \mathbf{A}_{2\ast}}\right\rbrack$$

As you may easily notice, the multiplication of a row vector and a column vector is closely related to the concept of dot product. Indeed, we have that

$$\mathbf{a}\cdot\mathbf{b}=\left\lbrack\matrix{a_x&a_y&a_z}\right\rbrack\left\lbrack\matrix{b_x\cr b_y\cr b_z}\right\rbrack=a_xb_x+a_yb_y+a_zb_z$$

That is, we can consider the operands of a dot product as row and column vectors, rather than just ordinary vectors.

What we have discussed so far in this section applies in general. That is, if we have a $1\times n$ row vector $\mathbf{u}$ and an $n\times p$ matrix $\mathbf{A}$, then the product $\mathbf{uA}$ is a linear combination of the rows of $\mathbf{A}$, with scalar coefficients given by the elements of $\mathbf{u}$.

$$\mathbf{uA}=\left\lbrack\matrix{u_0&u_1&\cdots&u_{n-1}}\right\rbrack\left\lbrack\matrix{A_{00}&\cdots&A_{0(n-1)}\cr \vdots&\ddots&\cdots\cr A_{(n-1)0}&\cdots&A_{(n-1)(n-1)}}\right\rbrack=u_0\mathbf{A}_{0\ast}+u_1\mathbf{A}_{1\ast}+\cdots+u_{n-1}\mathbf{A}_{(n-1)\ast}$$

We can also multiply an $m\times n$ matrix by an $n\times 1$ column vector. For example, if $\mathbf{A}$ is a $3\times 3$ matrix and $\mathbf{u}$ is a three-component vector that represent a column vector, we have that

$$\mathbf{Au}=\left\lbrack\matrix{A_{00}&A_{01}&A_{02}\cr A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}}\right\rbrack\left\lbrack\matrix{x\cr y\cr z}\right\rbrack=\left\lbrack\matrix{\mathbf{u}\cdot\mathbf{A}_{0\ast}\cr \mathbf{u}\cdot\mathbf{A}_{1\ast}\cr \mathbf{u}\cdot\mathbf{A}_{2\ast}}\right\rbrack$$

The result is a column vector with three elements where each entry is the dot product of $\mathbf{u}$ and a row of $\mathbf{A}$. Now, if we perform the dot product we have

$$\mathbf{Au}=x\mathbf{A}_{\ast 0}+y\mathbf{A}_{\ast 1}+z\mathbf{A}_{\ast 2}=\left\lbrack\matrix{\mathbf{A}_{\ast 0}&\mathbf{A}_{\ast 1}&\mathbf{A}_{\ast 2}}\right\rbrack\left\lbrack\matrix{x\cr y\cr z}\right\rbrack$$

Therefore, the product of a matrix and a column vector is a linear combination of the columns of the matrix, scaled by the elements of the column vector.


#### Properties of matrix multiplication

Since matrix multiplication is performed element-wise, it inherits the following properties from real numbers:

```{list-table}
:header-rows: 1

* - Name
  - Property
* - Associative
  - $(\mathbf{AB})\mathbf{C}=\mathbf{A}(\mathbf{BC})$
* - Distributive
  - $\mathbf{A}(\mathbf{B}+\mathbf{C})=\mathbf{AB}+\mathbf{AC}$
```
<br>

The commutative property does not apply for two reasons. Firstly, as mentioned earlier, matrix multiplication is only defined when the number of columns in the left matrix is equal to the number of rows in the right matrix. Secondly, even with square matrices of the same dimension (where matrix multiplication is always defined), swapping the order of the operands can lead to a different resultant matrix ─ consider equation $\eqref{eq:AMatrices1}$ and observe how each element of the resulting matrix is computed.

To conclude this section, as mentioned in [](vectors.md), we stated that the cross product can be computed by multiplying a row vector by a matrix. We can now verify this statement using the following equation.

$$\mathbf{w}=\mathbf{u}\times\mathbf{v}=\left\lbrack\matrix{v_x&v_y&v_z}\right\rbrack\left\lbrack\matrix{0&u_z&-u_y\cr -u_z&0&u_x\cr u_y&-u_x&0}\right\rbrack=(u_yv_z-u_zv_y,\ u_zv_x-u_xv_z,\ u_xv_y-u_yv_x)$$

Also, it's interesting to note that we can compute the orthogonal projection of a vector $\mathbf{v}$ onto a unit vector $\mathbf{n}$ by multiplying a row vector by a matrix.

$$\text{proj}_{\mathbf{n}}(\mathbf{v})=(\mathbf{v}\cdot\mathbf{n})\mathbf{n}=\left\lbrack\matrix{v_x&v_y&v_z}\right\rbrack\left\lbrack\matrix{n_x^2&n_xn_y&n_xn_z\cr n_xn_y&n_y^2&n_yn_z\cr n_xn_z&n_yn_z&n_z^2}\right\rbrack$$

Indeed, observe that the i-th component of $(\mathbf{v}\cdot\mathbf{n})\mathbf{n}$ is computed by multiplying $n_i$ with $(\mathbf{v}\cdot\mathbf{n})$. For instance, the first component of $(\mathbf{v}\cdot\mathbf{n})\mathbf{n}$ is 

$$n_x(v_xn_x+v_yn_y+v_zn_z)=(v_xn_x^2+v_yn_xn_y+v_zn_xn_z)$$

As you can verify, the same result is obtained in the vector-matrix multiplication above by executing the dot product between $\mathbf{v}$ and the first column of the matrix. The same applies to the other two components of $(\mathbf{v}\cdot\mathbf{n})\mathbf{n}$.

<br>

## Transpose of a matrix 

The transpose of a matrix $\mathbf{M}$ is often denoted with $\mathbf{M}^T$. The transpose can be derived from the original matrix by simply interchanging its rows and columns.

$$\mathbf{M}=\left\lbrack\matrix{1&2&3\cr 4&5&6\cr 7&8&9}\right\rbrack\quad\quad\quad\quad\mathbf{M^T}=\left\lbrack\matrix{1&4&7\cr 2&5&8\cr 3&6&9}\right\rbrack$$

As you can see, interchanging rows and columns in a matrix is equivalent to flipping the matrix over its diagonal. As a result, if we have a matrix $\mathbf{M}$, obtaining the ij-th entry of its transpose can be achieved by simply swapping the subscripts and specifying the ji-th entry

Below are the properties of the matrix transpose.

- $(\mathbf{A}^T)^T=\mathbf{A}$
- $(k\mathbf{A}^T)=k\mathbf{A}^T$
- $(\mathbf{A}+\mathbf{B})^T=\mathbf{A}^T+\mathbf{B}^T$
- $(\mathbf{AB})^T=\mathbf{B}^T\mathbf{A}^T$

```{admonition} Proof
:class: dropdown

The first three properties are straightforward to prove. For the last property, we can demonstrate it by computing the ji-th entry of both $(\mathbf{AB})^T$ and $\mathbf{B}^T\mathbf{A}^T$, and showing that they are equal. Specifically, we have:

$$
\begin{align*}
(\mathbf{AB})_{ij}^T&=(\mathbf{AB})_{ji}=\mathbf{A}_{j\ast}\cdot\mathbf{B}_{\ast i} \\
\\
(\mathbf{B}^T\mathbf{A}^T)_{ij}&=\mathbf{B}_{i\ast}^T\cdot\mathbf{A}_{\ast j}^T=\mathbf{B}_{\ast i}\cdot\mathbf{A}_{j\ast}
\end{align*}
$$

The two expressions are the same since the dot product is commutative. Also, observe how we flipped the subscripts to refer to the corresponding entry in the transposes.
```

<br>

## Identity matrix

The identity matrix is a square matrix in which all the elements are zero, except for the entries on the main diagonal, which are all 1. The main diagonal consists of elements $M_{ij}$ where $i=j$.

$$\left\lbrack\matrix{1&0\cr 0&1}\right\rbrack\quad\quad\quad\quad\left\lbrack\matrix{1&0&0\cr 0&1&0\cr 0&0&1}\right\rbrack\quad\quad\quad\quad\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr 0&0&0&1}\right\rbrack$$

The identity matrix is named as such because it serves as the multiplicative identity. That is, for an $n\times n$ matrix $\mathbf{M}$ and an identity matrix $\mathbf{I}$ of the same dimension, we have the following property:

$$\mathbf{MI} = \mathbf{IM} = \mathbf{M}$$

Observe that the multiplication with the identity matrix is commutative by definition, meaning that the order of multiplication does not matter. This is an exception to the general operation of matrix multiplication, where the order of multiplication typically matters and matrix multiplication is not commutative.

<br>

## Determinant of a matrix

The determinant of a matrix is closely related to the concept of hypervolume, which represents the measure of length in 1D, area in 2D, and volume in 3D. It is important to note that the determinant is only defined for square matrices. Now, you may be wondering what it means to have a signed length, area, or volume. As mentioned earlier in this tutorial (and as we will formally demonstrate in a subsequent appendix), matrices are associated with transformations, and the sign of the determinant indicates whether a transformation preserves or reverses the orientation of the standard basis vectors. In simpler terms, when multiplying (transforming) the standard basis vectors $\mathbf{i}$, $\mathbf{j}$, and $\mathbf{k}$ by a square matrix with a positive determinant, the handedness of the resulting coordinate system remains unchanged.

Before delving into the calculation of the determinant of a matrix, we first need to introduce the concept of matrix minors. By matrix minor of an $n\times n$ matrix $\mathbf{A}$ we mean the $(n−1)\times(n−1)$ matrix $\bar{\mathbf{A}}_{ij}$ derived from $\mathbf{A}$ by deleting the i-th row and j-th column. For example,

$$
\begin{align*}
&\mathbf{A}=\left\lbrack\matrix{A_{00}&A_{01}&A_{02}\cr A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}}\right\rbrack \\
\\
&{\bar{\mathbf{A}}}_{00}=\left\lbrack\matrix{A_{11}&A_{12}\cr A_{21}&A_{22}}\right\rbrack \\
\\
&{\bar{\mathbf{A}}}_{01}=\left\lbrack\matrix{A_{10}&A_{12}\cr A_{20}&A_{22}}\right\rbrack
\end{align*}
$$

Now, let's see how to calculate the determinant $\lvert\mathbf{A}\rvert$ (or $det\mathbf{A}$) of a square matrix $\mathbf{A}$. First of all, the determinant of a $1\times 1$ matrix is simply the only element in the matrix: $\lvert\mathbf{A}\rvert=A_{00}$, where $\mathbf{A}=\left\lbrack\matrix{A_{00}}\right\rbrack$. For matrices of higher dimensions, we can use the following recursive formula.

$$\begin{equation}\tag{2}det\mathbf{A}=\sum_{j=0}^{n-1}\ [(-1)^{i+j}\cdot A_{ij}\cdot det{\bar{\mathbf{A}}}_{ij}]\label{eq:AMatrices2}\end{equation}$$

Observe that the dot in the equation above represents a scalar multiplication, not a dot product. The only variable in the equation is $j$, allowing you to choose any $i$-th row of matrix $\mathbf{A}$ and apply equation $\eqref{eq:AMatrices2}$. Similarly, you can choose any $j$-th column of the matrix and apply equation $\eqref{eq:AMatrices2}$ by changing the variable under the summation symbol to $i$ instead of $j$.

Equation $\eqref{eq:AMatrices2}$ provides a recursive method for calculating the determinant of a matrix. It involves computing the determinants of matrix minors and using them in a linear combination, where the coefficients are the elements of the chosen i-th row. This approach allows us to extend the calculation of determinants from $1\times 1$ matrices to $2\times 2$ matrices by applying the formula above (we will use the elements of the first row of $\mathbf{A}$ as coefficients; that is, we set $i=0$).

$$\tag{3}
det\left\lbrack\matrix{A_{00}&A_{01}\cr A_{10}&A_{11}}\right\rbrack=A_{00}\ det[A_{11}]-A_{01}\ det\,[A_{10}]=A_{00}A_{11}-A_{01}A_{10}
\label{eq:AMatrices3}
$$

Now that we know how to calculate the determinants of $2\times 2$ matrices, we can compute the determinants of $3\times 3$ matrices as well.

$$
\begin{align*}
det\left\lbrack\matrix{A_{00}&A_{01}&A_{02}\cr A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}}\right\rbrack&=A_{00}\ det\left\lbrack\matrix{A_{11}&A_{12}\cr A_{21}&A_{22}}\right\rbrack-A_{01}\ det\left\lbrack\matrix{A_{10}&A_{12}\cr A_{20}&A_{22}}\right\rbrack+A_{02}\ det\left\lbrack\matrix{A_{10}&A_{11}\cr A_{20}&A_{21}}\right\rbrack \\
&=A_{00}A_{11}A_{22}-A_{00}A_{12}A_{21}-A_{01}A_{10}A_{22}+A_{01}A_{12}A_{20}+A_{02}A_{10}A_{11}-A_{02}A_{10}A_{21}
\end{align*}
$$

As can be observed, the result obtained is similar to the outcome of the scalar triple product discussed in [](vectors.md). This means that the determinant of a $3\times 3$ matrix $\mathbf{A}$ represents the signed volume of the parallelepiped formed by its three row vectors (or column vectors; indeed, note that $\lvert\mathbf{A}\rvert$=$\lvert\mathbf{A}^T\rvert$). Specifically, if the row vectors are denoted as $\mathbf{A}_{0\ast}$, $\mathbf{A}_{1\ast}$ and $\mathbf{A}_{2\ast}$, the sign of the determinant is positive if the vectors $\mathbf{A}_{0\ast}$ and $(\mathbf{A}_{1\ast}\times\mathbf{A}_{2\ast})$ are on the same side (half-space) with respect to the plane defined by $\mathbf{A}_{1\ast}$ and $\mathbf{A}_{2\ast}$. As will be further discussed in a later appendix, this property ensures that the handedness of a frame doesn't change if we use $\mathbf{A}$ to transform the standard basis vectors of the frame.

On the other hand, it's much easier to prove that the determinant of a $2\times 2$ matrix $\mathbf{A}$ is the signed area of the parallelogram formed by its two row vectors $\mathbf{A}_{0\ast}$ and $\mathbf{A}_{1\ast}$.

```{figure} images/02/det-signed-area.png
```

In the illustration above, we have that the area $T$ of the triangle formed by $\mathbf{A}_{0\ast}$ and $\mathbf{A}_{1\ast}$ is

$$T=A_{00}A_{11}-T_1-T_2-T_3$$

where

$$
\begin{align*}
T_1&=\frac{1}{2}A_{00}A_{01} \\
\\
T_2&=\frac{1}{2}(A_{11}-A_{01})(A_{00}-A_{10}) \\
\\
T_3&=\frac{1}{2}A_{10}A_{11}
\end{align*}
$$

Substituting and simplifying, we get

$$T=\frac{1}{2}A_{00}A_{11}-\frac{1}{2}A_{01}A_{10}$$

However, we want to calculate the area of the parallelogram $A_p$ formed by $\mathbf{A}_{0\ast}$ and $\mathbf{A}_{1\ast}$. For this purpose, we know that $A_p=2T$, so we have that $A_p$ is equal to equation $\eqref{eq:AMatrices3}$, the determinant of a $2\times 2$ matrix whose columns are $\mathbf{A}_{0\ast}$ and $\mathbf{A}_{1\ast}$. If you can rotate $\mathbf{A}_{0\ast}$ towards $\mathbf{A}_{1\ast}$ in the same way as $\mathbf{i}$ rotates towards $\mathbf{j}$ then the area is positive, otherwise it is negative.

By now, you might have figured out how to compute the determinant of a $4\times 4$ matrix.

$$
\begin{align*}
det\left\lbrack\matrix{A_{00}&A_{01}&A_{02}&A_{03}\cr A_{10}&A_{11}&A_{12}&A_{13}\cr A_{20}&A_{21}&A_{22}&A_{23}\cr A_{30}&A_{31}&A_{32}&A_{33}}\right\rbrack&=A_{00}\ det\left\lbrack\matrix{A_{11}&A_{12}&A_{13}\cr A_{21}&A_{22}&A_{23}\cr A_{31}&A_{32}&A_{33}}\right\rbrack-A_{01}\ det\left\lbrack\matrix{A_{10}&A_{12}&A_{13}\cr A_{20}&A_{22}&A_{23}\cr A_{30}&A_{32}&A_{33}}\right\rbrack \\
\\
&+\ A_{02}\ det\left\lbrack\matrix{A_{10}&A_{11}&A_{13}\cr A_{20}&A_{21}&A_{23}\cr A_{30}&A_{31}&A_{33}}\right\rbrack-A_{03}\ det\left\lbrack\matrix{A_{10}&A_{11}&A_{12}\cr A_{20}&A_{21}&A_{22}\cr A_{30}&A_{31}&A_{32}}\right\rbrack
\end{align*}
$$

It can be proven that if we add a scaled column (or row) of a matrix to another column (or row) of the same matrix, the determinant does not change. However, we won't provide a formal proof here as a practical example is more than enough. Indeed, below you can verify that if we scale the second column of a simple $2\times 2$ matrix by $3$, and add the result to the first column, the determinant doesn't change.

$$
\begin{align*}
det\left\lbrack\matrix{1&2\cr 3&4}\right\rbrack&=(1)(4)-(2)(3)=4-6=-2 \\
\\
det\left\lbrack\matrix{(1+3(2))&2\cr (3+3(4))&4}\right\rbrack&=(7)(4)-(2)(15)=28-30=-2
\end{align*}
$$

<br>

## Adjoint of a matrix 

The cofactor $C_{ij}$ of the entry $A_{ij}$ in an $n\times n$ matrix $\mathbf{A}$ can be defined as follows

$$C_{ij}=(-1)^{i+j}\ det{\bar{\mathbf{A}}}_{ij}$$

Computing the cofactor of every element of the matrix $\mathbf{A}$, we can create the cofactor matrix $\mathbf{C}_\mathbf{A}$ of $\mathbf{A}$, where $C_{ij}$ is the element of $\mathbf{C}_\mathbf{A}$ at the ij-th position.

$\mathbf{C}_\mathbf{A}=\left\lbrack\matrix{C_{00}&\cdots&C_{0n}\cr \vdots&\ddots&\vdots\cr C_{n0}&\cdots&C_{nn}}\right\rbrack$

The adjoint $\mathbf{A}^\ast$ of the matrix $\mathbf{A}$ is simply the transpose of its cofactor matrix: $\mathbf{A}^\ast=\mathbf{C}_\mathbf{A}^T$. 
So, we have that the ij-th element of $\mathbf{A}^\ast$ is

$$\tag{4}
C_{ij}^T=(-1)^{i+j}\ det\bar{\mathbf{A}}_ {ji}
\label{eq:AMatrices4}
$$

Please note the swap between subscripts in the matrix minor to select the corresponding entry in the transpose.

The adjoint of a matrix, and in particular the equation $\eqref{eq:AMatrices4}$, is useful to compute the inverse of the matrix.

<br>

## Inverse of a matrix [WIP]

<br>