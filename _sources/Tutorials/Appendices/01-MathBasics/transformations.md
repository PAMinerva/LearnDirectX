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

That's exactly what we have seen in [](matrices.md). Indeed, if we perform the multiplication between the row vector $\mathbf{v}$ and the matrix $\mathbf{M}$, we have

$$
\begin{align*}
\mathbf{vM}&=\left\lbrack\matrix{\mathbf{v}\cdot\mathbf{M}_{\ast0}&\mathbf{v}\cdot\mathbf{M}_{\ast1}&\mathbf{v}\cdot\mathbf{M}_{\ast2}}\right\rbrack \\
\\
&=\left\lbrack\matrix{xM_{00}+yM_{10}+zM_{20}&xM_{01}+yM_{11}+zM_{21}&xM_{02}+yM_{12}+zM_{22}}\right\rbrack \\
\\
&=\left\lbrack\matrix{xM_{00}&xM_{01}&xM_{02}}\right\rbrack+\left\lbrack\matrix{yM_{10}&yM_{11}&yM_{12}}\right\rbrack+\left\lbrack\matrix{zM_{20}&zM_{21}&zM_{22}}\right\rbrack \\
\\
&=x\mathbf{M}_{0\ast}+y\mathbf{M}_{1\ast}+z\mathbf{M}_{2\ast}=x\mathbf{f}+y\mathbf{g}+z\mathbf{h} =\mathbf{w}
\tag{1}
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

(transforms-scaling-label)=
### Scaling 

In a 3D Cartesian coordinate system, vectors can be scaled independently in three directions by scaling their respective components. That is, given a scaling $S$ and a vector $\mathbf{v}=(x,y,z)$ we have

$$S(\mathbf{v})=(s_xx, s_yy, s_zz)$$

Here, $s_x$, $s_y$, and $s_z$ represent the scaling factors associated with the x, y, and z directions, respectively. This allows us to stretch or shrink vectors in each direction independently, altering their overall size and proportions.

The scaling is uniform if the scaling factors are equal $(s_x=s_y=s_z)$, otherwise it's non-uniform. We can formally prove the scaling is a linear transformation by showing that it satisfies the two conditions of linearity.

```{admonition} Proof
:class: dropdown

$$
\begin{flalign}
S(\mathbf{u}+\mathbf{v})&=\big( s_x(u_x+v_x),\ s_y(u_y+v_y),\ s_z(u_z+v_z) \big) \\ \\
&=(s_xu_x+s_xv_x,\ s_yu_y+s_yv_y,\ s_zu_z+s_zv_z) \\ \\
&=(s_xu_x,\ s_yu_y,\ s_zu_z)+(s_xv_x,\ s_yv_y,\ s_zv_z) \\ \\
&=S(\mathbf{u})+S(\mathbf{v}) \\ \\
\\
S(k\mathbf{v})&=(s_xkv_x,\ s_ykv_y,\ s_zkv_z) \\ \\
&=k(s_xv_x,\ s_yv_y,\ s_zv_z) \\ \\
&=kS(\mathbf{v})
\end{flalign}
$$
```

We know that a linear transformation can be associated with a matrix whose rows are the transformed standard basis vectors. In 3D Cartesian coordinate systems, we have that

$$
\begin{align*}
S(\mathbf{i})&=(s_x1,\ s_y0,\ s_z0)=(s_x,0,0) \\ \\
S(\mathbf{j})&=(s_x0,\ s_y1,\ s_z0)=(0,s_y,0) \\ \\
S(\mathbf{k})&=(s_x0,\ s_y0,\ s_z1)=(0,0,s_z)
\end{align*}
$$

Then, the matrix $\mathbf{S}$ associated with a scaling is

$$\mathbf{S}=\left\lbrack\matrix{s_x&0&0\cr 0&s_y&0\cr 0&0&s_z}\right\rbrack$$

This matrix is associated with the scaling operation. Consequently, we can apply scaling to any 3D vector by multiplying it with the scaling matrix $\mathbf{S}$. If the intention is to restore the vector to its original size, we can accomplish this by multiplying the scaled vector by the inverse of the scaling matrix, denoted as $\mathbf{S}^{-1}$.

$$S^{-1}=\left\lbrack\matrix{1/s_x&0&0\cr 0&1/s_y&0\cr 0&0&1/s_z}\right\rbrack$$

````{prf:example}

Given a minimum point $\mathbf{p}=(-2,0,-2)$ and a maximum point $\mathbf{q}=(2,0,2)$ defining a square, if you want to scale it by a factor of $2$ along the x-axis, $0.5$ along the z-axis, and leave the y-coordinate unchanged, you can use the corresponding scaling matrix:

$$\mathbf{S}=\left\lbrack\matrix{2&0&0\cr 0&1&0\cr 0&0&0.5}\right\rbrack$$

To scale the square, we need to multiply both $\mathbf{p}$ and $\mathbf{q}$ by $\mathbf{S}$.

$$
\begin{align*}
\left\lbrack\matrix{-2&0&-2}\right\rbrack\left\lbrack\matrix{2&0&0\cr 0&1&0\cr 0&0&0.5}\right\rbrack&=\left\lbrack\matrix{-4&0&-1}\right\rbrack \\ 
\\
\left\lbrack\matrix{2&0&2}\right\rbrack\left\lbrack\matrix{2&0&0\cr 0&1&0\cr 0&0&0.5}\right\rbrack&=\left\lbrack\matrix{4&0&1}\right\rbrack
\end{align*}
$$

The following illustration shows the result of these transformations

```{figure} images/03/scaling.png
```
````


### Rotation 

A rotation is a linear transformation, but a formal proof of this won't be provided since it is simpler to observe, from the illustration below, that the rotation of the sum of two vectors (that is, the rotation of the diagonal of the parallelogram defined by the two vectors) is equivalent to the sum of rotations of the two vectors (that is, the diagonal of the rotated parallelogram). At the same time, rotating a uniformly scaled vector is equivalent to first rotating the vector and then applying the uniform scaling.

```{figure} images/03/rotation-linear.png
```

Finding the matrix associated with a rotation can be slightly more challenging compared to scaling. However, for the purpose of our discussion, we will consider $R_\mathbf{n}(\mathbf{v})$ as a clockwise rotation of a vector $\mathbf{v}$ around a unit vector $\mathbf{n}$ by an angle $\theta$. That is, you will see the vector $\mathbf{v}$ rotating clockwise when looking down the positive direction of the unit vector $\mathbf{n}$, which determines the axis of rotation. Obviously, the result of $R_\mathbf{n}(\mathbf{v})$ is the rotated version of the vector $\mathbf{v}$ passed as an argument. 

If a counterclockwise rotation is desired, the angle $\theta$ can simply be negated. Additionally, if $\mathbf{n}$ is not a unit vector, we can always normalize it, allowing us to extend our results to the more general case.

Now, let's consider the left side of the illustration below.

```{figure} images/03/rotation2.png
```

From [](vectors.md), we know that $\mathbf{n}\times\mathbf{v}$ is orthogonal to the plane defined by $\mathbf{n}$ and $\mathbf{v}$. During the rotation, the vector $\mathbf{v}$ traces out an arc of a circle, which is also orthogonal to the plane defined by $\mathbf{n}$ and $\mathbf{v}$. Therefore, the vector $\mathbf{n}\times\mathbf{v}$ and the circle drawn by $\mathbf{v}$ lie in the same plane.

Now, let's consider the projection of the vector $\mathbf{v}$ onto $\mathbf{n}$. It can be expressed as the difference between two vectors; this result can also be derived from [](vectors-gram-schmidt-label).

$$\text{proj}_\mathbf{n}(\mathbf{v})=\mathbf{v}-\mathbf{v}_\bot$$

where $\mathbf{v}_\bot$ is the orthogonal component of $\mathbf{v}$ with respect to $\mathbf{n}$. Therefore, we have that

$$\mathbf{v}_\bot=\mathbf{v}-\text{proj}_\mathbf{n}(\mathbf{v})$$

The vector $\mathbf{v}_\bot$ lies in the plane defined by $\mathbf{n}$ and $\mathbf{v}$ (since it's the subtraction of these two vectors) and therefore it's orthogonal to $\mathbf{n}\times\mathbf{v}$. $\ R_\mathbf{n}(\mathbf{v}_\bot)$ is the orthogonal component of $R_\mathbf{n}(\mathbf{v})$ with respect to $\mathbf{n}$. Moreover, observe that

$$\vert\mathbf{n}\times\mathbf{v}\vert=\vert\mathbf{n}\vert\vert\mathbf{v}\vert\sin a=\vert\mathbf{v}\vert\sin a =\vert\mathbf{v}_\bot\vert$$

That is, $\mathbf{n}\times\mathbf{v}$ and $\mathbf{v}_\bot$ have the same length, and are orthogonal to each other. So, they can define a 2D frame (just like the standard basis vectors $\mathbf{i}$ and $\mathbf{j}$) where we can compute the coordinates of $R_\mathbf{n}(\mathbf{v}_\bot)$, which can be considered as the rotation of the basis vector $\mathbf{v}_\bot$ about $\mathbf{n}$. This means that $R_\mathbf{n}(\mathbf{v}_\bot)$ has the same length of both $\mathbf{n}\times\mathbf{v}$ and $\mathbf{v}_\bot$. As you can see in the image above (on the right), the orthogonal projection of $R_\mathbf{n}(\mathbf{v}_\bot)$ onto $\mathbf{v}_\bot$ is

$$\displaystyle\frac{(R_\mathbf{n}(\mathbf{v}_\bot)\cdot\mathbf{v}_\bot)}{\vert\mathbf{v}_\bot\vert^2}\mathbf{v}_\bot=\frac{\vert R_\mathbf{n}(\mathbf{v}_\bot)\vert\vert\mathbf{v}_\bot\vert\cos\theta}{\vert\mathbf{v}_\bot\vert^2}\mathbf{v}_\bot=\frac{\vert\mathbf{v}_\bot\vert\vert\mathbf{v}_\bot\vert\cos\theta}{\vert\mathbf{v}_\bot\vert^2}\mathbf{v}_\bot=\cos{\mathbf{\theta}}\ \mathbf{v}_\bot$$

In a similar way, the orthogonal projection of $R_\mathbf{n}(\mathbf{v} _\bot)$ onto $\mathbf{n}\times\mathbf{v}$ is

$$\displaystyle\frac{(R_\mathbf{n}(\mathbf{v}_\bot)\cdot\mathbf{n}\times\mathbf{v})}{\vert\mathbf{n}\times\mathbf{v}\vert^2}\mathbf{n}\times\mathbf{v}=\frac{\vert R_\mathbf{n}(\mathbf{v}_\bot)\vert\vert\mathbf{n}\times\mathbf{v}\vert\cos{(90°-\theta)}}{\vert\mathbf{n}\times\mathbf{v}\vert^2}\mathbf{n}\times\mathbf{v}=\frac{\vert\mathbf{n}\times\mathbf{v}\vert\vert\mathbf{n}\times\mathbf{v}\vert\sin\theta}{\vert\mathbf{n}\times\mathbf{v}\vert^2}\mathbf{n}\times\mathbf{v}=\sin\theta\ \mathbf{n}\times\mathbf{v}$$

Therefore, we have that

$$R_\mathbf{n}(\mathbf{v}_\bot)=\cos\theta\ \mathbf{v}_\bot+\sin\theta\ (\mathbf{n}\times\mathbf{v})$$

Now, we can compute $R_\mathbf{n}(\mathbf{v})$ as a sum of $\text{proj}_\mathbf{n}(\mathbf{v})$ and $R_\mathbf{n}(\mathbf{v}_\bot)$.

$$
\begin{align*}
R_{\mathbf{n}}(\mathbf{v})&=\text{proj}_\mathbf{n}(\mathbf{v})+R_{\mathbf{n}}(\mathbf{v_\bot}) \\ 
\\
&=(\mathbf{n}\cdot \mathbf{v})\mathbf{n}+\cos\theta\ \mathbf{v}_\bot+\sin\theta\ (\mathbf{n}\times\mathbf{v}) \\
\\
&=(\mathbf{n}\cdot \mathbf{v})\mathbf{n}+\cos\theta\ (\mathbf{v}-(\mathbf{n}\cdot\mathbf{v})\mathbf{n})+\sin\theta\ (\mathbf{n}\times\mathbf{v}) \\
\\
&=\cos\theta\ \mathbf{v}+(1-\cos\theta)(\mathbf{n}\cdot\mathbf{v})\mathbf{n}+\sin\theta\ (\mathbf{n}\times\mathbf{v})
\end{align*}
$$

As you can see, the orthogonal component $\mathbf{v}_\bot$ has disappeared from the equation, so we can compute any rotation by knowing only the vector to rotate $(\mathbf{v})$, the angle of rotation $(\theta)$, and the unit vector $(\mathbf{n})$ indicating the axis of rotation. 

At this point, we can transform the standard basis vectors to compute the row vectors that make up the rotation matrix $\mathbf{R}_\mathbf{n}$.

$$
\begin{align*}
R_\mathbf{n}(\mathbf{i})&=\big(\cos\theta+(1-\cos\theta)\ x^2,\quad(1-\cos\theta)\ xy+\sin\theta\ z,\quad(1-\cos\theta)\ xz-\sin\theta\ y\big) \\ 
\\
R_\mathbf{n}(\mathbf{j})&=((1-\cos\theta)\ xy-\sin\theta\ z,\quad \cos\theta+(1-\cos\theta)\ y^2,\quad(1-\cos\theta)\ yz+\sin\theta\ x) \\
\\
R_\mathbf{n}(\mathbf{k})&=((1-\cos\theta)\ xz+\sin\theta\ y,\quad(1-\cos\theta)\ yz-\sin\theta\ x,\quad\cos\theta+(1-\cos\theta)\ z^2)
\end{align*}
$$

where $\mathbf{n}=(x, y, z)$, $\mathbf{i}=(1,0,0)$, $\mathbf{j}=(0,1,0)$, and $\mathbf{k}=(0,0,1)$. <br>
Therefore, we have that

$$\mathbf{R}_\mathbf{n}=\left\lbrack\matrix{c+(1-c)x^2&(1-c)xy+sz&(1-c)xz-sy\cr (1-c)xy-sz&c+(1-c)y^2&(1-c)yz+sx\cr (1-c)xz+sy&(1-c)yz-sx&c+(1-c)z^2}\right\rbrack$$

where $c=\cos\theta$, and $s=sin\theta$.<br>
For example, if we want to rotate about the x-, y- and z- axes, we need to set $\mathbf{n}=\mathbf{i}=(1,0,0)$, $\mathbf{n}=\mathbf{j}=(0,1,0)$, and $\mathbf{n}=\mathbf{k}=(0,0,1)$, respectively. So, the corresponding rotation matrices are

$$\mathbf{R}_x=\left\lbrack\matrix{1&0&0\cr 0&\cos\theta&\sin\theta\cr 0&-\sin\theta&\cos\theta}\right\rbrack\quad\quad\quad\mathbf{R}_y=\left\lbrack\matrix{\cos\theta&0&-\sin\theta\cr 0&1&0\cr \sin\theta&0&\cos\theta}\right\rbrack\quad\quad\quad\mathbf{R}_z=\left\lbrack\matrix{\cos\theta&\sin\theta&0\cr -\sin\theta&\cos\theta&0\cr 0&0&1}\right\rbrack$$

````{prf:example}

Given a minimum point $\mathbf{p}=(-2,0,-2)$ and a maximum point $\mathbf{q}=(2,0,2)$ of a square, suppose you want to rotate it $-45°$ clockwise (that is, $45°$ counterclockwise ) about the y-axis. The corresponding rotation matrix is:

$$\mathbf{R}_y=\left\lbrack\matrix{\cos\theta&0&-\sin\theta\cr 0&1&0\cr \sin\theta&0&\cos\theta}\right\rbrack=\left\lbrack\matrix{\cos{(-45°)}&0&-\sin{(-45°)}\cr 0&1&0\cr \sin{(-45°)}&0&\cos{(-45°)}}\right\rbrack=\left\lbrack\matrix{1/\sqrt{2}&0&1/\sqrt{2}\cr 0&1&0\cr -1/\sqrt{2}&0&1/\sqrt{2}}\right\rbrack$$

To rotate the square we need to multiply $\mathbf{p}$ and $\mathbf{q}$ by the rotation matrix $\mathbf{R}_y$.

$$
\begin{align*}
\left\lbrack\matrix{-2&0&-2}\right\rbrack\left\lbrack\matrix{1/\sqrt{2}&0&1/\sqrt{2}\cr 0&1&0\cr -1/\sqrt{2}&0&1/\sqrt{2}}\right\rbrack&=\left\lbrack\matrix{0&0&-2.83}\right\rbrack \\ 
\\
\quad\left\lbrack\matrix{2&0&2}\right\rbrack\left\lbrack\matrix{1/\sqrt{2}&0&1/\sqrt{2}\cr 0&1&0\cr -1/\sqrt{2}&0&1/\sqrt{2}}\right\rbrack&=\left\lbrack\matrix{0&0&2.83}\right\rbrack
\end{align*}
$$

The following illustration shows the result of these transformations

```{figure} images/03/rotation.png
```
````

You can easily verify that the rows of $\mathbf{R}_x$ are unit vectors and orthogonal to each other (i.e., they make up an orthonormal set). The same applies to the rows of $\mathbf{R}_y$, $\mathbf{R}_z$ and the general rotation matrix $\mathbf{R}_\mathbf{n}$ (although, this last one is a little trickier to prove). A matrix whose rows compose an orthonormal set is called orthogonal. Orthogonal matrices are of particular interest since their inverse is equal to their transpose. That is, we have that

$$\mathbf{R}_\mathbf{n}^{-1}=\mathbf{R}_\mathbf{n}^T=\left\lbrack\matrix{c+(1-c)x^2 & (1-c)xy-sz & (1-c)xz+sy \cr  (1-c)xy+sz & c+(1-c)y^2 & (1-c)yz-sx \cr  (1-c)xz-sy & (1-c)yz+sx & c+(1-c)z^2}\right\rbrack$$

<br>

## Affine transformations 

Now that we know how to scale and rotate vectors, we'd also like to move them. However, we face two challenges when it comes to moving vectors:

- We certainly can use translation to move bound vectors (points, e.g., vertex positions) to relocate 3D objects in the scene. However, for free vectors, this transformation doesn't make any sense since direction and magnitude don't change after a translation (the point of application for free vectors is irrelevant). Therefore, we need a way to distinguish between points (bound vectors) and (free) vectors.

- Moving a point can't be expressed as a linear combination of the standard basis vectors. In other words, translation is not a linear transformation, so we can't associate a $3\times 3$ matrix with it. Fortunately, we can still find a way to incorporate translations into our matrix equation $\mathbf{w}=\mathbf{vM}$.

Affine transformations extend linear ones by adding translations. The next section will explore this type of transformation, aiming to resolve the aforementioned issues.


### Translation

To move a bound vector (point) $\mathbf{p}=(p_x, p_y, p_z)$ using a translation $T$, we simply add a displacement vector $\mathbf{t}=(t_x, t_y, t_z)$ to the point.

$$T(\mathbf{p})=\mathbf{p}+\mathbf{t}=(p_x+t_x,\ p_y+t_y,\ p_z+t_z)\tag{2}\label{eq:ATransforms2}$$

For example, let's consider a 2D point $\mathbf{p}=(3, 3)$. If we want to translate it by $+5$ units along the x-axis and $+2$ units along the y-axis, we can add the displacement vector $\mathbf{t}=(5, 2)$ to $\mathbf{p}$, as shown in the following illustration.

```{figure} images/03/translation.png
```

At the same time, we can translate the frame by using the same displacement vector $\mathbf{t}$, that now specifies the offset where to move the origin of the translated frame A (the starting one) with respect to the original frame B (the new one). In frame A, the coordinates of the bound vector (point) $\mathbf{p}$ are still the same, while in frame B the coordinates of $\mathbf{p}$ can be seen as the sum of $\mathbf{p}$ and $\mathbf{t}$.

```{figure} images/03/translation2.png
```

So, it seems that translation of vectors and translation of coordinate systems are mathematically equivalent, just like with linear transformations (in the next section we will formally prove that this is true in general for affine transformations). This means that we can translate a frame to apply the same transformation to vectors defined in that frame. So far so good, but the question is: can we find a $3\times 3$ matrix $\mathbf{T}$ to associate with translations in 3D spaces? Unfortunately, the answer is no. 

To understand why, just take a look again at the definition of $T(\mathbf{p})$ provided in equation $\eqref{eq:ATransforms2}$. It's a simple sum of vectors. On the other hand, in a vector-matrix multiplication, we have the dot product of two vectors (the row vector and a column of the matrix). Therefore, there is no way we can find a $3\times 3$ matrix $\mathbf{T}$ so that $\mathbf{w}=\mathbf{p}+\mathbf{t}=\mathbf{pT}$. This is due to the fact that translations are not linear transformations.

```{admonition} Proof
:class: dropdown

$$
\begin{align*}
T(\mathbf{u}+\mathbf{v})&=(u_x+v_x+t_x,\ u_y+v_y+t_y,\ u_z+v_z+t_z) \\ 
\\
T(\mathbf{u})+T(\mathbf{v})&=(u_x+t_x,\ u_y+t_y,\ u_z+t_z)+(v_x+t_x,\ v_y+t_y,\ v_z+t_z) \\
&=(u_x+v_x+2t_x,\ u_y+v_y+2t_y,\ u_z+v_z+2t_z)
\end{align*}
$$

Therefore, $T(\mathbf{u}+\mathbf{v}) \neq T(\mathbf{u})+T(\mathbf{v})$, indicating that the first condition of linearity does not hold.
```

However, we can still find a way to incorporate translations into our matrix equation $\mathbf{w}=\mathbf{vM}$ by employing homogeneous coordinates. This allows us to mantain the form $\mathbf{w}=\mathbf{p}+\mathbf{t}=\mathbf{pT}$, where $\mathbf{T}$ is a translation matrix.

<br>

## Homogeneous coordinates 

As explained in [](vectors.md), homogeneous coordinates introduce an "extra" coordinate. This means that starting from a 3D Cartesian systems, we step into the 4-th dimension. Fortunately, we just pop in to pick up what we need, and leave immediately after.

A point in 3D space can be represented in homogeneous coordinates by the tuple $(x,y,z,w)$, where the related 3D Cartesian coordinates are $(x/w,\ y/w,\ z/w,\ w/w)$. So, the generic 3D Cartesian coordinates $(x, y, z)$ can be written as $(x, y, z, 1)$ in homogeneous coordinates. This way, we can use the first three components as regular 3D Cartesian coordinates, and the last component to differentiate between points and vectors.

- Points: $\ (x, y, z, 1)$

- Vectors: $\ (x, y, z, 0)$

We need this distinction between points and vectors as we want to apply translations to points without affecting vectors. Now, since we are using four components, we can try to find a $4\times 4$ matrix $\mathbf{T}$ associated with translations in 3D spaces. Let's start with the identity matrix, using the offset of the translated frame $\mathbf{t}=(t_x, t_y, t_z, 1)$ as the last row.

$$\mathbf{T}=\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr t_x&t_y&t_z&1}\right\rbrack$$

Let's see what happens if we multiply a generic point $\mathbf{p}=(p_x, p_y, p_z, 1)$ by $\mathbf{T}$.

$$\mathbf{w}=\mathbf{pT}=\left\lbrack\matrix{p_x & p_y & p_z & 1}\right\rbrack\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr t_x & t_y & t_z & 1}\right\rbrack=(p_x+t_x,\ p_y+t_y,\ p_z+t_z,\ 1)=\mathbf{p}+\mathbf{t}$$

That's exactly the definition of $T(\mathbf{p})$ in equation $\eqref{eq:ATransforms2}$, but expressed in homogeneous coordinates (that is, with the last component equal to $1$). Now, let's see what happens if we multiply a generic vector $\mathbf{v}=(v_x, v_y, v_z, 0)$ by $\mathbf{T}$.

$$\mathbf{w}=\mathbf{vT}=\left\lbrack\matrix{v_x & v_y & v_z & 0}\right\rbrack\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr t_x & t_y & t_z & 1}\right\rbrack=(v_x, v_y, v_z, 0)=\mathbf{v}$$

The vector $\mathbf{v}$ is not affected by the translation. It looks like we just found a matrix associated with translation of points, but that does not affect directions. Of course, the inverse of the translation matrix is

$$\mathbf{T}^{-1}=\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr -t_x&-t_y&-t_z&1}\right\rbrack$$

However, we'd like to also include linear transformations (scaling and rotation). For this purpose, we can try to embed the $3\times 3$ matrix $\mathbf{A}$ associated with linear transformations in the upper left position of $\mathbf{T}$ as follows:

$$\mathbf{M}=\left\lbrack\matrix{A_{00}&A_{01}&A_{02}&0\cr A_{10}&A_{11}&A_{12}&0\cr A_{20}&A_{21}&A_{22}&0\cr t_x&t_y&t_z&1}\right\rbrack$$

Now, if we multiply a generic vector $\mathbf{v}=(x, y, z, 0)$ by $\mathbf{M}$ we have

$$\mathbf{w}=\mathbf{vM}=\left\lbrack\matrix{x&y&z&0}\right\rbrack.\left\lbrack\matrix{\mathbf{f}\cr \mathbf{g}\cr \mathbf{h}\cr \mathbf{t}}\right\rbrack=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}+0\mathbf{t}=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}$$

where $\mathbf{f}=(A_{00}, A_{01}, A_{02}, 0)$, $\mathbf{g}=(A_{10}, A_{11}, A_{12}, 0)$ and $\mathbf{h}=(A_{20}, A_{21}, A_{22}, 0)$ are the linearly transformed standard basis vectors $\mathbf{i}=(1,0,0,0)$, $\mathbf{j}=(0,1,0,0)$ and $\mathbf{k}=(0,0,1,0)$, while $\mathbf{t}=(t_x, t_y, t_z, 1)$ is still the position of the translated frame (the starting one) with respect to the original frame (the new one).

As you can see, the translation doesn't affect the vector, and the result is the same we showed in equation $\eqref{eq:ATransforms1}$: $\mathbf{w}=\mathbf{vM}$. Indeed, when you transform vectors with $\mathbf{M}$, these can only be affected by the linear transformation embedded within the matrix. Moreover, the result is still a vector: the last component is zero because it's the result of the dot product between $\mathbf{v}=(v_x, v_y, v_z, 0)$ and $(0,0,0,1)$, the last column of $\mathbf{M}$.

On the other hand, if we multiply a generic point $\mathbf{q}=(x, y, z, 1)$ by $\mathbf{M}$ we have

$$\mathbf{p}=\mathbf{q}\mathbf{M}=\left\lbrack\matrix{x&y&z&1}\right\rbrack.\left\lbrack\matrix{\mathbf{f}\cr \mathbf{g}\cr \mathbf{h}\cr \mathbf{t}}\right\rbrack=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}+1\mathbf{t}=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}+\mathbf{t}
\tag{3}
\label{eq:ATransforms3}$$

Here, we transform $\mathbf{q}$ with a linear transformation, which results in an intermediate vector $\mathbf{q}_1=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}$ to which we add the translation $\mathbf{t}$. That's exactly what we stated in equation $\eqref{eq:ATransforms2}$: a translation is the sum of a point ad a vector. However, in equation $\eqref{eq:ATransforms3}$ the point is $\mathbf{t}$, as it specifies the origin of the starting frame with respect to the new one. Also, the result is still a point: the last component is $1$ because it's the result of the dot product between $\mathbf{q}=(x, y, z, 1)$ and $(0, 0, 0, 1)$, the last row of $\mathbf{M}$.

Furthermore, the difference of two points is the vector from a point to the other one. Indeed, from equation $\eqref{eq:ATransforms3}$, we have that

$$\mathbf{p}-\mathbf{t}=x\mathbf{f}+y\mathbf{g}+z\mathbf{h}$$

which is a vector, as it's not bound to the origin of the frame of reference (see the illustration below, on the left side). On the other hand, the sum of two points is a point as well, but it doesn't make any sense (unlike the sum of two vectors, which is the resultant force, direction or speed).

```{figure} images/03/vector-point-sum.png
```

In conclusion, the matrix $\mathbf{M}$ we built in this section can be used to transform both vectors and points with affine transformations, as it embeds a linear transformation, plus a translation. The first three rows represent the transformed standard basis vectors, while the four-th is a displacement that specifies the origin of the starting frame with respect to a new one. Most importantly, $\mathbf{M}$ still allows us to express the coordinates of a vector in a starting frame with respect to a new one. As explained at the beginning of the tutorial, this implies that $\mathbf{M}$ is a matrix that allows us to go from a frame to another, which means that change of coordinates and change of coordinate system are still mathematically equivalent in the context of affine transformations.

<br>

## Composition of transformations 

Imagine you want to scale, rotate and translate a vector $\mathbf{v}$, obtaining a vector $\mathbf{w}$. Mathematically, this can be expressed in matrix form as follows:

$$\mathbf{w}=((\mathbf{vS})\mathbf{R})\mathbf{T})\tag{4}\label{eq:ATransforms4}$$

The vector $\mathbf{v}$ and the resultant vector $\mathbf{w}$ can be considered row vectors of dimension $1\times n$, while the matrices $\mathbf{S}$, $\mathbf{R}$ and $\mathbf{T}$ are all $n\times n$.

In vector-matrix multiplication, we need to perform $(2n-1)$ operations for each component of the resultant vector: $(n-1)$ sums and $n$ products. So, to obtain the total operations involved, we must multiply $(2n-1)$ by the $n$ components of the resultant vector. Therefore, we have $n(2n-1)$ operations to perform each vector-matrix multiplication. In equation $\eqref{eq:ATransforms4}$, we need to perform three of these multiplications, so the total cost is $3n(2n-1)=6n^2-3n=O(n^2)$.

However, thanks to the associative property of matrix multiplication we can re-write equation $\eqref{eq:ATransforms4}$ as follows:

$$\mathbf{w}=(((\mathbf{vS})\mathbf{R})\mathbf{T})=(\mathbf{v}(\mathbf{SR})\mathbf{T})=\mathbf{v}(\mathbf{SRT})\tag{5}\label{eq:ATransforms5}$$

Now, we have two matrix multiplications and a vector-matrix multiplication.<br>
Each matrix multiplication needs $(2n-1)$ operations for each element of the resultant matrix. So, to obtain the total operations involved, we must multiply $(2n-1)$ by the $n^2$ elements of the resultant matrix. Therefore, we have $(2n-1)n^2$ operations to perform a matrix multiplication. In equation $\eqref{eq:ATransforms5}$, we have two of these multiplications, plus a vector-matrix multiplication, so the total cost is $2(2n^3-n^2)+n(2n-1)=4n^3-n=O(n^3)$.

It seems that we need more operations to perform if we first multiply the matrices. However, imagine you want to transform 10 thousand vectors by the same matrices $\mathbf{S}$, $\mathbf{R}$ and $\mathbf{T}$. In the first case we need to perform $10000(3n(2n-1))$ operations, while in the second case we only need $10000(n(2n-1))+2(2n^3-n^2)$ operations, as the double matrix multiplication $(\mathbf{SRT})$ must be computed only once. That is, we can reuse the result of $(\mathbf{SRT})$ as a matrix to transform every vector. For example, if $n=4$, we have $10000(3n(2n-1))=840\text{k}$, while $10000(n(2n-1))+2(2n^3-n^2)=280\text{k}$.

Then, it is strongly recommended to transform vectors with a matrix which is a composition of all the transformations you want to apply to the vectors.

<br>

## Transformation in DirectX

DirectXMath offers convenient helper functions for constructing $4\times 4$ matrices to transform (i.e., scale, rotate and translate) both points (e.g., vertex positions) and vectors (e.g., light directions) in homogeneous coordinates. These functions often come in two flavors:

- **Scalar implementation**: Written in standard C++, utilizing scalar operations. It works on any CPU regardless of its architecture, but may be slower than alternative options.

- **SIMD-accelerated implementation**: Leverages the power of Single Instruction, Multiple Data (SIMD) instructions available on modern CPUs, exposed by intrinsics functions provided by the Microsoft C++ compiler. This allows the CPU to perform calculations on multiple data elements simultaneously, resulting in significant performance gains.

**XMMatrixScaling** returns a matrix associated with a scaling operation, similar to the one examined in [](transforms-scaling-label).

```{code-block} cpp

inline XMMATRIX XM_CALLCONV XMMatrixScaling
(
    float ScaleX,
    float ScaleY,
    float ScaleZ
) noexcept
{
#if defined(_XM_NO_INTRINSICS_)

    XMMATRIX M;
    M.m[0][0] = ScaleX;
    M.m[0][1] = 0.0f;
    M.m[0][2] = 0.0f;
    M.m[0][3] = 0.0f;

    M.m[1][0] = 0.0f;
    M.m[1][1] = ScaleY;
    M.m[1][2] = 0.0f;
    M.m[1][3] = 0.0f;

    M.m[2][0] = 0.0f;
    M.m[2][1] = 0.0f;
    M.m[2][2] = ScaleZ;
    M.m[2][3] = 0.0f;

    M.m[3][0] = 0.0f;
    M.m[3][1] = 0.0f;
    M.m[3][2] = 0.0f;
    M.m[3][3] = 1.0f;
    return M;

#elif defined(_XM_SSE_INTRINSICS_)
    XMMATRIX M;
    M.r[0] = _mm_set_ps(0, 0, 0, ScaleX);
    M.r[1] = _mm_set_ps(0, 0, ScaleY, 0);
    M.r[2] = _mm_set_ps(0, ScaleZ, 0, 0);
    M.r[3] = g_XMIdentityR3.v;
    return M;

#endif
}

```

In the previous code snippet, `g_XMIdentityR3` is a **XMVECTORF32** global constant defined as `{0.0f, 0.0f, 0.0f, 1.0f}`. In contrast, the `_mm_set_ps` intrinsic function returns a **__m128** value initialized (in a single instruction) using the floating-point values provided as arguments. Below, you'll find the declaration of the `_mm_set_ps` function, along with a concise explanation of how its parameters map to the bits of the return value (denoted as `dst`). In particular, observe that the last parameter `e0` is used to initialize the least significant 32 bits of the returned **__m128** value, corresponding to the bits of the first component of a 4-component vector of floating-point values.

```
__m128 _mm_set_ps (float e3, float e2, float e1, float e0);

dst[31:0] = e0
dst[63:32] = e1
dst[95:64]  = e2
dst[127:96] = e3
```

<br>

[WIP]

<br>