# Transformations

In graphics applications, it is common to apply transformations such as scaling, rotation, and translation to objects before displaying them on the screen. For example, let's consider a scenario where you want to move an object to a different position in the scene. In order to achieve this, you need to move all the vertices of the object by applying a transformation on them. Matrices are mathematical entities that represent transformations. This means that if we treat vertex positions as vectors in a 3D space, we can multiply them by a matrix to change their position, orientation and magnitude in the scene. In the example given, we would perform a matrix multiplication between each vertex position $\mathbf{v}=(x,y,z)$ stored in the vertex buffer and a translation matrix $T$ in order to change its coordinates.

$$T(\mathbf{v})=(x\textrm', y\textrm', z\textrm')=\mathbf{w}$$

Here, $\mathbf{w}$ represents the new position of the vertex $\mathbf{v}$ after the transformation $T$. <br>
Now, it's interesting how we can visualize it in two different ways. Obviously, we can move (translate) $\mathbf{v}$ until its coordinate match those of $\mathbf{w}$. Alternatively, we can translate the entire frame to achieve the same result. In [](vectors.md), we established that points (and therefore positions) are vectors bound to the origin of a frame, which can be described by the coordinates of the corresponding arrowheads. In the illustration below, on the left side, we translate the arrowhead of $\mathbf{v}$ by changing its coordinates, resulting in the vector\position $\mathbf{w}$. On the right side of the illustration, we apply the same translation to the origin of the frame. As you can observe, the coordinates of $\mathbf{v}$ with respect to the transformed frame A remain the same because the vector shares the same fate (transformation) as its frame of reference. However, now the original frame B "see" $\mathbf{v}$ as being applied to the origin of frame A. Consequently, the coordinates of $\mathbf{v}$ with respect to the original frame B are the same as those of $\mathbf{w}$. Thus, we can consider the transformed frame A as the starting frame, while the original frame B as the new frame of reference.

```{figure} images/03/transformation.png
```

The same concept applies when rotating or scaling a vertex position. As we will explore in this tutorial, vector transformations and changes of coordinate systems are mathematically equivalent. In other words, transforming an object is equivalent to transforming its frame of reference, and vice versa.

<br>

## Linear transformations 

A transformation $L$ is linear if it satisfies the following two conditions for any two vectors $\mathbf{u}$ and $\mathbf{v}$, and any scalar $k$.

$$
\begin{align*}
L(\mathbf{u}+\mathbf{v})&=L(\mathbf{u})+L(\mathbf{v}) \\
\\
L(k\mathbf{v})&=kL(\mathbf{v})
\end{align*}
$$

In [](vectors.md), it was demonstrated that a generic bound vector $\mathbf{v}=(x,y,z)$ can be expressed as a linear combination as follows:

$$\mathbf{v}=x\mathbf{i}+y\mathbf{j}+z\mathbf{k}$$

Here, $\mathbf{i}$, $\mathbf{j}$, and $\mathbf{k}$ are the basis vectors that describe a frame, and $x$, $y$, and $z$ are the coefficients that determine the magnitude and direction of $\mathbf{v}$ along each basis vector. Therefore, following the linearity conditions illustrated above, we can express the transformation of $\mathbf{v}$ as:

$$L(\mathbf{v})=L(x\mathbf{i}+y\mathbf{j}+z\mathbf{k})=xL(\mathbf{i})+yL(\mathbf{j})+zL(\mathbf{k})=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}=\mathbf{w}$$

Here, $\mathbf{f}$, $\mathbf{g}$, and $\mathbf{h}$ represent the transformed standard basis vectors. These vectors correspond to the standard basis vectors of the transformed frame. It's important to note that the coordinates of $\mathbf{f}$, $\mathbf{g}$, and $\mathbf{h}$ are expressed with respect to the original frame. For example, in the illustration below, a linear transformation $L$ is applied to the standard basis vectors $\mathbf{i}=(1,0)$ and $\mathbf{j}=(0,1)$ of a frame B. This create a new frame A (since the transformed standard basis vector will represent the standard basis vector in a new coordinate system). However, observe that, in the transformed frame A, the coordinates of the transformed $\mathbf{i}$ remain $(1,0)$ ─ the same applies to $L(\mathbf{i})$ in frame A. On the other hand, we have $L(\mathbf{i})=\mathbf{f}=(x',y')$ in frame B, which represents the original frame ─ again, the same applies to $L(\mathbf{i})$ in frame B. Therefore, we can consider the transformed frame A as our initial starting point, where $\mathbf{i}$ and $\mathbf{j}$ were $(1,0)$ and $(0,1)$, while the frame B represents the final result after applying the transformation $L$ to both $\mathbf{i}$ and $\mathbf{j}$.

```{figure} images/03/linear-transformation.png
```

Then, we can conclude that applying a linear transformation to a vector $\mathbf{v}$ is equivalent to applying the same transformation to the initial frame, represented by its standard basis vectors. This is crucial because it means that transforming a vector $\mathbf{v}$ with a linear transformation can be achieved by transforming the standard basis vectors of the frame in which the vector is defined. 

Furthermore, it has been shown that the coordinates of the transformed vector $\mathbf{w}$ can be obtained as a linear combination of the transformed basis vectors, with the components of $\mathbf{v}$ used as coefficients. From [](matrices.md), we know that a linear combination can be expressed as the product of a row vector and a column vector, which is related to the concept of dot product. Therefore, we can express the transformation as follows:

$$
\begin{align*}
\mathbf{w}=L(\mathbf{v})&=L(x\mathbf{i}+y\mathbf{j}+z\mathbf{k})=xL(\mathbf{i})+yL(\mathbf{j})+zL(\mathbf{k})=x\mathbf{f}+y\mathbf{g}+z\mathbf{h} \\
\\
&=\mathbf{v}\cdot\mathbf{m}=\left\lbrack\matrix{x&y&z}\right\rbrack\left\lbrack\matrix{\gets&\mathbf{f}&\rightarrow\cr \gets&\mathbf{g}&\rightarrow\cr \gets&\mathbf{h}&\rightarrow}\right\rbrack \\
\\
&=\mathbf{vM}=\left\lbrack\matrix{x&y&z}\right\rbrack\left\lbrack\matrix{M_{00}&M_{01}&M_{02}\cr M_{10}&M_{11}&M_{12}\cr M_{20}&M_{21}&M_{22}}\right\rbrack \\
\\
&=\left\lbrack\matrix{\mathbf{v}\cdot\mathbf{M}_{\ast 0}&\mathbf{v}\cdot\mathbf{M}_{\ast 1}&\mathbf{v}\cdot\mathbf{M}_{\ast 2}}\right\rbrack
\end{align*}
$$

where $\mathbf{v}=(x, y, z)$, $\mathbf{m}=(\mathbf{f},\mathbf{g},\mathbf{h})$, $\mathbf{f}=\left\lbrack\matrix{M_{00}&M_{01}&M_{02}}\right\rbrack$, $\mathbf{g}=\left\lbrack\matrix{M_{10}&M_{11}&M_{12}}\right\rbrack$ and $\mathbf{h}=\left\lbrack\matrix{M_{20}&M_{21}&M_{22}}\right\rbrack$.

That’s exactly what we have seen in [](matrices.md). Indeed, if we perform the multiplication between the row vector $\mathbf{v}$ and the matrix $\mathbf{M}$, we have

$$
\begin{align*}
\mathbf{vM}&=\left\lbrack\matrix{\mathbf{v}\cdot\mathbf{M}_{\ast0}&\mathbf{v}\cdot\mathbf{M}_{\ast1}&\mathbf{v}\cdot\mathbf{M}_{\ast2}}\right\rbrack \\
\\
&=\left\lbrack\matrix{xM_{00}+yM_{10}+zM_{20}&xM_{01}+yM_{11}+zM_{21}&xM_{02}+yM_{12}+zM_{22}}\right\rbrack \\
\\
&=\left\lbrack\matrix{xM_{00}&xM_{01}&xM_{02}}\right\rbrack+\left\lbrack\matrix{yM_{10}&yM_{11}&yM_{12}}\right\rbrack+\left\lbrack\matrix{zM_{20}&zM_{21}&zM_{22}}\right\rbrack \\
\\
&=x\mathbf{M}_{0\ast}+y\mathbf{M}_{1\ast}+z\mathbf{M}_{2\ast}=x\mathbf{f}+y\mathbf{g}+z\mathbf{h} =\mathbf{w}\tag{1}
\label{eq:ATransforms1}
\end{align*}
$$

We just found that to transform a vector $\mathbf{v}$ we need to multiply it by a matrix $\mathbf{M}$ whose rows are the transformed standard basis vectors. In other words, we can express this relationship as:

$$\mathbf{w}=\mathbf{vM}$$

where $\mathbf{M}$ is the matrix that transforms a vector $\mathbf{v}$ into another vector $\mathbf{w}$.<br>
However, we can also interpret $\mathbf{M}$ as the matrix to go from a frame A (the starting one) to a frame B (the new one), which allows a vector $\mathbf{v}$ bound to the origin of frame A to be expressed with respect to frame B. We can demonstrate this intricated conclusion with a simple illustration.

```{figure} images/03/linear-transformation2.png
```

On the left side of the image above, we have a vector $\mathbf{p}_A=(3,2)$ applied to the origin of a frame A. Recall that a vector can be expressed as a linear combination of the standard basis vectors, using its components as coefficients. Thus, in frame A, we have:

$$\mathbf{p}_A=3\mathbf{i}+2\mathbf{j}$$

This representation corresponds to the diagonal of the parallelogram formed by scaling the vectors $\mathbf{i}$ and $\mathbf{j}$ using the components of $\mathbf{p}_A$ as coefficients. 

Now, imagine scaling frame A twice along both the x-axis and y-axis, followed by a rotation around the origin. This transformation results in the vector $\mathbf{p}_A$ being doubled in size and rotated with respect to the original frame, which can therefore be considered as a new frame B because in the transformed one (A) the coordinates of $\mathbf{p}_A$ are still $(3,2)$. As stated earlier, $L(\mathbf{i})$ and $L(\mathbf{j})$ are expressed in coordinates with respect to the original frame B. Now, our goal is to demonstrate that using these transformed basis vectors as rows of a matrix, we can compute the coordinates of $\mathbf{p}_A$ with respect to frame B. In other words, we aim to show that this matrix enables us to transition from frame A to frame B. As depicted on the right side of the illustration above, we have that

$$\mathbf{p}_B=3L(\mathbf{i})+2L(\mathbf{j})$$

That is, $\mathbf{p}_A$ with respect to the frame B is the diagonal of the transformed standard basis vectors, scaled using the components of $\mathbf{p}_A$. In other words, the coordinates of $\mathbf{p}_A$ with respect to frame B can be expressed as a linear combination of the transformed basis vectors, using the components of $\mathbf{p}_A$ as coefficients. This is exactly what we formally proved in equation $\eqref{eq:ATransforms1}$, where we used the transformed basis vectors as rows of the matrix $\mathbf{M}$.

Thus, we have just demonstrated that $\mathbf{M}$ is the matrix to express the coordinates of a vector in a frame A with respect to a frame B. That is, we have shown that $\mathbf{M}$ allows to go from frame A (the original one) to frame B (the transformed one). At this point, it should come as no surprise that the inverse matrix $\mathbf{M}^{-1}$ represents the transformation to go from frame B back to frame A.


### Scaling [WIP]

<br>