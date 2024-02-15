# Vectors

## Coordinate systems

A coordinate system, often referred to as a reference frame (or simply as a frame or system for brevity), is a fundamental concept in mathematics and geometry. It provides a way to uniquely determine a position in space by using one or more numbers called coordinates.

To define a coordinate system, three key components are needed: an origin, a unit of measurement, and a positive direction. The origin is a reference point from which all positions are measured. The unit of measurement determines the scale or size of each coordinate value. The positive direction indicates how the coordinates increase from the origin.

By using a coordinate system, we can bridge the gap between geometry and numerical problems. Geometrical problems involving positions, distances, angles, and shapes can be translated into numerical problems by assigning coordinates to the geometric elements. Similarly, numerical problems can be visualized and understood geometrically by mapping the numbers that represent points and other geometric entities onto a coordinate system.



### 1D Coordinate system

A 1D coordinate system is a reference frame that operates along a single dimension, typically represented by a straight line (but it can also be a curve). In a 1D coordinate system, an origin point is typically defined as a reference point for the line. This origin point serves as the starting point from which positions are measured. Additionally, it is necessary to establish a unit of measurement that determines the scale or increment used to quantify distances or positions along the line, and a positive direction indicating the way in which values increase from the origin. In this type of coordinate system, a position $\mathbf{P}$ on a line (or curve) can be determined with a single number (coordinate) $x$.

```{figure} images/01/1D-coord-system.png
```

For instance, in the line illustrated above, $x=3$ specifies that the position $\mathbf{P}$ is $3$ units away from the origin $\mathbf{O}$ in the positive direction along the line. This approach assigns a unique coordinate to each point\position on the line, and every real number representing the coordinate of a distinct point on the line. Therefore, the coordinate of a point $\mathbf{P}$ is defined as the signed distance between the origin $\mathbf{O}$ and the point.


### Cartesian coordinate system

In two dimensiones (i.e., in a plane), two perpendicular lines (also called axes) define a system. We can express the coordinates $(x, y)$ of a point $\mathbf{P}$ as the signed distances between the origin $\mathbf{O}$ and the projection of $\mathbf{P}$ onto the axes.

```{figure} images/01/2D-coord-system.png
```

For example, point $\mathbf{P}$ in the illustration above has coordinates $(2, 3)$ because its projection is two units away from $\mathbf{O}$ along the x-axis and three units away from $\mathbf{O}$ along the y-axis.

In three dimensions, three mutually perpendicular axes define a system. The coordinates $(x, y, z)$ of a point represent the signed distances between the origin $\mathbf{O}$ and the projections of the point onto the axes.

```{note}
To project a point $\mathbf{P}$ onto the x-axis, imagine first projecting the point perpendicularly onto the xz-plane, bringing you back to the 2D case. Then, project the result onto the x-axis. A similar approach can be used to compute the projection onto the other two axes.
```

You can also view the coordinates $(x, y, z)$ as a series of steps from the origin $\mathbf{O}$ to point $\mathbf{P}$:

1) Move $x$ units along the x-axis.
2) From that position, move $y$ units parallel to the y-axis.
3) Finally, move $z$ units parallel to the z-axis.

```{figure} images/01/3D-coord-system.png
```

Depending on the direction and order of the axes, a three-dimensional system can be right-handed or left-handed.

```{figure} images/01/handedness.png
```

Typically, the y-axis points up, the x-axis points right, and the z-axis points forward (note that forward aims at different directions depending on whether a right-handed or left-handed coordinate system is being used). However, this isn't a strict rule. Sometimes, you can have the z-axis points up, and in that case the y-axis points forward. You can always switch from a y-up to a z-up configuration with a simple transformation, but there's no point in providing further details here as we will use z-up or y-up configurations consistently during the rendering process (further details will be provided as needed). 

It seems that DirectX programmers commonly use left-handed systems, so we'll adopt this convention in this tutorial series. However, remember that right-handed systems are also valid for use in DirectX.


### Polar coordinate system

In a two-dimensional plane, we can define a coordinate system called the polar coordinate system. This system relies on two key elements:

- Origin (or pole): A fixed reference point.
- Polar axis: A ray extending from the origin.

A point $\mathbf{P}$ is identified by its polar coordinates, $(r,\theta)$, where:

- $r$: Represents the distance from the origin to point P. This distance is also called the radius.
- $θ$: Represents the angle between the polar axis and the line segment connecting the origin and point P. This angle is usually measured in degrees or radians ($2\pi$ radians =$360°$).

```{figure} images/01/polar-coord.png
```

```{note}
A single point can have multiple polar coordinate representations due to the periodicity of angles. For example, $(r,\theta)$ and $(r,\theta + 2\pi)$ represent the same point. Be aware of this when working with polar, cylindrical or spherical coordinates.
```


### Cylindrical and spherical coordinate systems 

Extending polar coordinates, we can add an extra y-coordinate to specify the height from the plane containing origin and polar axis. This way, we can locate all points on a cylinder in three dimensions, hence the name "cylindrical coordinates" represented by the triple $(r, y, \theta)$.

```{figure} images/01/cylindrical-coord.png
```

Build upon cylindrical coordinates, we can obtain spherical coordinates by converting the height $y$ into and angle $\varphi$ within the range $[0, 180°]$. This gives us the triple $(r, \varphi, \theta)$, which identifies all points on a sphere. The following illustration shows the conversion between spherical and Cartesian coordinates. Observe that the angle $\varphi$ is measured from the upward direction.

```{figure} images/01/cartesian2spherical.png
```


### Homogeneous coordinate system

In homogeneous coordinates, a point in a plane can be represented by a triple $(x, y, z)$. By dividing each coordinate by the z-coordinate, we can obtain the corresponding Cartesian coordinates: $(x/z,\ y/z,\ z/z)$. Obviously, this approach introduces an additional coordinate, even though typically only two coordinates are used to specify a point in a two-dimensional space. Nevertheless, homogeneous coordinates enable us to express various transformations, such as scaling, rotation, and translation, in a simple and consistent way using matrices.

```{important}
In general, a homogeneous coordinate system is a frame where only the ratios of the coordinates are significant, rather than their actual values.
```

```{note}
After performing the division to obtain the Cartesian coordinates, the last coordinate will always be 1. This concept extends to three-dimensional spaces as well. Therefore, if we have the 3D Cartesian coordinates $(x, y, z)$, we can also express them as $(x, y, z, 1)$.
```

<br>

## Vectors

Vectors are mathematical objects that represent quantities with both magnitude and direction. They are used in many fields, including physics and computer graphics. For example, vectors can be used to specify the position and displacement of objects, as well as their speed. They can also describe the direction and intensity of forces acting on objects or light rays emitted by a light source, as well as surface normals.


### Definition

Geometrically, a vector can be visualized as an arrow. The length of the arrow represents the magnitude or size of the vector, while the direction in which the arrow points indicates the direction of the vector.

```{figure} images/01/geo-vector.png
```

When working with vectors for numerical computation on a computer, the geometric definition provided above is not as important, although. Fortunately, we can express vectors numerically using tuples, which are finite sequences of numbers. To establish this numerical representation, we need to bind vectors to the origin of a Cartesian coordinate system. In other words, we translate a vector while maintaining its magnitude and direction until its starting point aligns with the origin.

```{figure} images/01/num-vector.png
```

At that point, we can use the coordinates $(x, y)$ of the head of a generic vector $\mathbf{v}$ in a 2D Cartesian system as the numerical representation of the vector in that system. That is, we can write $\mathbf{v}=(x, y)$, with $x$ and $y$ called components of the vector. Similarly, for a vector $\mathbf{v}$ in a 3D Cartesian system, the numerical representation would be $(x, y, z)$. Then, we can use $\mathbf{v}$ to specify a point in a frame (maybe to set the position of an object, or a target location for movement).

```{figure} images/01/vector-point.png
```

When only magnitude and direction of a vector matter, then the point of application is of no importance, and the vector is called **free vector**. On the other hand, a vector bound to the origin of a system is called **bound vector**. The numerical representation of a bound vector is only valid in the system where it is bound. If the same free vector is bound to different systems, its numerical representation will change accordingly.

```{figure} images/01/vector-free-bound.png
```

```{important}
This is an important point to remember: if you define a vector numerically, its coordinates are relative to a specific reference frame. Therefore, we can conclude that two vectors $\mathbf{u}=(u_x, u_y, u_z)$ and $\mathbf{v}=(v_x, v_y, v_z)$ are equal only if $u_x=v_x$, $u_y=v_y$ and $u_z=v_z$. In other words, they have equal coordinates if bound to the origin of the same frame.
```


### Basic operations 

Vectors support various interesting operations, including addition, subtraction, and three types of multiplication.


#### Addition and Subtraction

Numerically, the sum of two vectors $\mathbf{u}=(u_x, u_y, u_z)$ and $\mathbf{v}=(v_x, v_y, v_z)$ is defined as

$$\mathbf{u}+\mathbf{v}=(u_x+v_x,\ u_y+v_y,\ u_z+v_z)$$

The addition may be represented geometrically by placing the tail of the arrow representing $\mathbf{v}$ at the head of the arrow representing $\mathbf{u}$, and then drawing an arrow from the tail of $\mathbf{u}$ to the head of $\mathbf{v}$. This arrow represent a new vector as the sum of $\mathbf{u}$ and $\mathbf{v}$. Alternatively, we can bind $\mathbf{u}$ and $\mathbf{v}$ to a shared point and then draw the diagonal of the parallelogram with sides $\mathbf{u}$ and $\mathbf{v}$.

```{figure} images/01/vector-sum.png
```

The difference between two vectors $\mathbf{u}$ and $\mathbf{v}$ is defined as

$$\mathbf{u}-\mathbf{v}=(u_x-v_x,\ u_y-v_y,\ u_z-v_z)$$

Geometrically, the vector subtraction can be defined by bounding $\mathbf{u}$ and $\mathbf{v}$ to a shared point and drawing an arrow from the head of $\mathbf{v}$ to the head of $\mathbf{u}$. Alternatively, it can be treated as a sum $(\mathbf{u}+(-\mathbf{v}))$ by placing the tail of the inverse of $\mathbf{v}$ at the head of $\mathbf{u}$. At that point, we can draw an arrow from the tail of $\mathbf{u}$ to the head of $-\mathbf{v}$. This arrow represent a new vector as the difference between $\mathbf{u}$ and $\mathbf{v}$. The inverse of a vector will be formally defined in the next section.

```{figure} images/01/vector-sub.png
```

#### Scalar multiplication

We can multiply a vector $\mathbf{v}=(x, y, z)$ by a real number $k$ called scalar. Numerically, this operation is defined as

$$k\mathbf{v}=k(x,y,z)=(kx,ky,kz)$$

Geometrically, this is equivalent to scaling a vector, which is why real numbers are often called scalars. If $k$ is negative, the resulting vector will aim in the opposite direction as well. In particular, if $k=−1$ we get the inverse $-\mathbf{v}$ of a vector $\mathbf{v}$, which aims in the opposite direction without changing its length.

```{figure} images/01/vector-mul.png
```

##### Properties of addition and scalar multiplication

Here are some of the properties of vector addition and scalar multiplication:

|                         |                                                                                                                      |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Commutative (vector)    | $\mathbf{u}+\mathbf{v}=\mathbf{v}+\mathbf{u}$                                                                        |
| Associative (vector)    | $(\mathbf{u}+\mathbf{v})+\mathbf{w}=\mathbf{u}+(\mathbf{v}+\mathbf{w})$                                              |
| Additive Identity       | $\mathbf{v}+\mathbf{0}=\mathbf{v}$  (where $\mathbf{0}$ is the zero (or null) vector, whose components are all zero) |
| Distributive (vector)   | $k(\mathbf{u}+\mathbf{v})=k\mathbf{u}+k\mathbf{v}$                                                                   |
| Distributive (scalar)   | $(k+t)\mathbf{v}=k\mathbf{v}+t\mathbf{v}$                                                                            |
| Associative (scalar)    | $k(t\mathbf{v})=(kt)\mathbf{v}$                                                                                      |
| Multiplicative Identity | $1\mathbf{v}=\mathbf{v}$   (where $k=1$ is a scalar)                                                                 |


##### Length of a vector

Now, we can define the length of a vector $|v|$ (or $\|v\|$) as a scalar value that represents the magnitude of the vector. Consider the following illustration.

```{figure} images/01/vector-length.png
```

We have that $a$ is the length of the projection of the vector $\mathbf{v}$ onto the xz-plane. From the Pythagorean theorem, $a=\sqrt{x^2+z^2}$. Then, applying the same theorem once again, we have that

$$|\mathbf{v}|=\sqrt{y^2+a^2}=\sqrt{y^2+(\sqrt{x^2+z^2})^2}=\sqrt{x^2+y^2+z^2}$$

Sometimes, only the direction of a vector is relevant. In such cases, we can normalize the vector to ensure its length becomes 1. Usually, the symbol $\hat{\mathbf{v}}$ is used to indicate a unit vector (a vector with length 1). To normalize a vector $\mathbf{v}=(x, y, z)$, we can multiply it by the reciprocal of its magnitude.

$$\displaystyle\hat{\mathbf{v}}=\frac{\mathbf{v}}{|\mathbf{v}|}=\left(\frac{x}{|\mathbf{v}|},\frac{y}{|\mathbf{v}|},\frac{z}{|\mathbf{v}|}\right)$$

We can verify that $\hat{\mathbf{v}}$ is a unit vector by computing its length.

$$\displaystyle|\hat{\mathbf{v}}|=\sqrt{\left(\frac{x}{|\mathbf{v}|}\right)^2+\left(\frac{y}{|\mathbf{v}|}\right)^2+\left(\frac{z}{|\mathbf{v}|}\right)^2}=\frac{\sqrt{x^2+y^2+z^2}}{\sqrt{|\mathbf{v}|^2}}=\frac{|\mathbf{v}|}{|\mathbf{v}|}=1$$

Three significant unit vectors are: $\mathbf{i}=(1,0,0)$, $\mathbf{j}=(0,1,0)$ and $\mathbf{k}=(0,0,1)$. These vectors have unit lengths and align with the x-, y-, and z-axes, respectively, in a 3D Cartesian coordinate system. They are commonly referred to as the **standard basis vectors** of a coordinate system.

```{figure} images/01/basis-vectors.png
```

#### Dot product

The dot product is a form of vector multiplication that results in a scalar value ─ it is also known as the scalar product for this reason. The dot product of two vector $\mathbf{u}=(u_x, u_y, u_z)$ and $\mathbf{v}=(v_x, v_y, v_z)$ is defined as

$$\mathbf{u}\cdot\mathbf{v}=u_xv_x+u_yv_y+u_zv_z$$

In essence, the dot product of two vectors is obtained by summing the products of the corresponding components.


##### Properties of dot multiplication

Below are some of the properties of the dot product:

|                           |                                                                                                                                         |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Commutative               | $\mathbf{u}\cdot\mathbf{v}=\mathbf{v}\cdot\mathbf{u}$                                                                                   |
| Distributive              | $(\mathbf{u}+\mathbf{v})\cdot\mathbf{w}=\mathbf{w}\cdot(\mathbf{u}+\mathbf{v})=(\mathbf{w}\cdot\mathbf{u})+(\mathbf{w}\cdot\mathbf{v})$ |
| Square of a vector length | $\|\mathbf{v}\| ^2=v_x^2+v_y^2+v_z^2=\mathbf{v}\cdot\mathbf{v}$                                                                         |

<br>

The associative property doesn't apply because $\mathbf{w}\cdot(\mathbf{u}\cdot\mathbf{v})$ is not defined. Indeed, the dot product is defined as an operation between two vectors, but $(\mathbf{u}\cdot\mathbf{v})$ yields a scalar value.

Moreover, from the law of cosines $c^2=a^2+b^2-2ab\cos{\theta}$ (a proof is provided at the end of the section), we can show that

$$\begin{equation}\tag{1}\mathbf{v}\cdot\mathbf{u}=|\mathbf{v}| |\mathbf{u}|\cos{\theta}\label{eq:Avectors1}\end{equation}$$

Indeed, if we set $a=|\mathbf{u}|$, $b=|\mathbf{v}|$ and $c=|\mathbf{u}-\mathbf{v}|$ we have that

$$
\begin{align*} c^2 &= a^2 + b^2 - 2ab \cos{\theta} \\ 
|\mathbf{u-v}|^2 &= |\mathbf{u}|^2 + |\mathbf{v}|^2 - 2|\mathbf{u}| |\mathbf{v}|\cos{\theta} \\ 
(\mathbf{u} - \mathbf{v}) \cdot (\mathbf{u} - \mathbf{v}) &= \mathbf{u} \cdot \mathbf{u} + \mathbf{v} \cdot \mathbf{v} - 2|\mathbf{u}| |\mathbf{v}|\cos{\theta} \\ 
\mathbf{u} \cdot\mathbf{u} -2(\mathbf{u} \cdot \mathbf{v}) + \mathbf{v} \cdot \mathbf{v} &= \mathbf{u} \cdot \mathbf{u} + \mathbf{v} \cdot \mathbf{v} - 2|\mathbf{u}| |\mathbf{v}|\cos{\theta} \\
 \mathbf{u}\cdot \mathbf{v} &= |\mathbf{u}| |\mathbf{v}|\cos{\theta} \end{align*}
$$

From equation $\eqref{eq:Avectors1}$, we can derive other properties. For example,

- If $(\mathbf{u}\cdot\mathbf{v}) = 0$ then the angle $\theta$ between $\mathbf{u}$ and $\mathbf{v}$ is $90°$ (that is, they are orthogonal: $\mathbf{u}\ \bot\ \mathbf{v}$)
- If $(\mathbf{u}\cdot\mathbf{v}) > 0$ then the angle $\theta$ between $\mathbf{u}$ and $\mathbf{v}$ is less than $90°$
- If $(\mathbf{u}\cdot\mathbf{v}) < 0$ then the angle $\theta$ between $\mathbf{u}$ and $\mathbf{v}$ is greater than $90°$

To conclude this section, we will prove the law of cosines: $c^2=a^2+b^2-2ab\cos{\theta}$.

````{admonition} Proof
:class: dropdown

Let $\mathbf{a}$ be the vector from $C$ to $B$, $\mathbf{b}$ the vector from $C$ to $A$, and $\mathbf{c}$ the vector from $A$ to $B$.

```{figure} images/01/law-cosines.png
```

We have that 

$\begin{align*}&\mathbf{c}=\mathbf{a}-\mathbf{b} && \text{(subtration of two vectors)}\end{align*}$

Squaring both sides and simplifying

$|\mathbf{c}|^2=|\mathbf{a}-\mathbf{b}|^2$

$
\begin{align*}
&|\mathbf{c}|^2=(\mathbf{a}-\mathbf{b})\cdot (\mathbf{a}-\mathbf{b}) && \text{(square of a vector length)} \\
&|\mathbf{c}|^2=|\mathbf{a}|^2+|\mathbf{b}|^2-2\ \mathbf{a}\cdot\mathbf{b} && \text{(distributive law of the dot product)} \\
&|\mathbf{c}|^2=|\mathbf{a}|^2+|\mathbf{b}|^2-2|\mathbf{a}||\mathbf{b} | \cos{\theta} && \text{(from equation \eqref{eq:Avectors1})}
\end{align*}
$
````

##### Orthogonal projection

The orthogonal projection of a vector $\mathbf{v}$ onto another vector $\mathbf{n}$ is defined as the vector $\text{proj}_\mathbf{n}(\mathbf{v})$. Consider the following illustration.

```{figure} images/01/vector-proj.png
```

From trigonometry, we know that, in a right triangle, the cosine of an angle equals the ratio between the length of the adjacent side and the length of the hypotenuse. Therefore, we can calculate the length of the adjacent side by multiplying the length of the hypotenuse by the cosine of the angle between the adjacent side and the hypotenuse. Considering the image above, we have that $|\text{adj}|=|\text{proj}_\mathbf{n}(\mathbf{v})|$ and $|\text{hyp}|=|\mathbf{v}|$. So, if $\mathbf{n}$ is a unit vector, we can write

$$\text{proj}_\mathbf{n}(\mathbf{v})=(|\mathbf{v}|\cos{\theta})\mathbf{n}=(|\mathbf{v}|1\cos{\theta})\mathbf{n}=(|\mathbf{v}||\mathbf{n}|\cos{\theta})\mathbf{n}=(\mathbf{v}\cdot\mathbf{n})\mathbf{n}$$

with $(\mathbf{v}\cdot\mathbf{n})$ signed length of the projection, and $\mathbf{n}$ that indicates its direction. This means that $(\mathbf{v}\cdot\mathbf{n})$ can invert the direction of projection if $\theta > 90°$. Anyway, this gives us a geometrical interpretation of the dot product, at least if $\mathbf{n}$ is a unit vector. If that's not the case, we can always normalize $\mathbf{n}$ to make it unit length. Then, we can replace $\mathbf{n}$ with its normalized version $\mathbf{n}/|\mathbf{n}|$, giving us the more general formula

$$\text{proj}_\mathbf{n}(\mathbf{v})=\left(\mathbf{v}\cdot\displaystyle\frac{\mathbf{n}}{|\mathbf{n}|}\right)\displaystyle\frac{\mathbf{n}}{|\mathbf{n}|}=\displaystyle\frac{(\mathbf{v}\cdot\mathbf{n})}{|\mathbf{n}|^2}\mathbf{n}$$

Thanks to the concept of orthogonal projection, we can express any bound vector $\mathbf{v}$ as the sum of its projections onto the standard basis vectors as follows:

$$\mathbf{v}=(\mathbf{v}\cdot\mathbf{i})\mathbf{i}+(\mathbf{v}\cdot\mathbf{j})\mathbf{j}+(\mathbf{v}\cdot\mathbf{k})\mathbf{k}=x\mathbf{i}+y\mathbf{j}+z\mathbf{k}=x(1, 0, 0)+y(0, 1, 0)+z(0, 0, 1)=(x, y, z)$$

Indeed, we have

$$
\begin{flalign}
&(\mathbf{v}\cdot\mathbf{i})&=(x, y, z)\cdot(1, 0, 0)&=x \\
&(\mathbf{v}\cdot\mathbf{j})&=(x, y, z)\cdot(0, 1, 0)&=y \\
&(\mathbf{v}\cdot\mathbf{k})&=(x, y, z)\cdot(0, 0, 1)&=z
\end{flalign}
$$

Also, note that $(x\mathbf{i}+z\mathbf{k})$ is the projection of $\mathbf{v}$ onto the xz-plane, so that we can sum this projection with $y\mathbf{j}$ to get $\mathbf{v}$.

```{figure} images/01/vector-proj2.png
```

```{note}
You can also see it as a sum of scaled vectors: we scale $\mathbf{i}$, $\mathbf{j}$ and $\mathbf{k}$ with the corresponding components of $\mathbf{v}$. Indeed, the diagonal of the parallelogram defined by $x\mathbf{i}$ and $z\mathbf{k}$ is $(x\mathbf{i}+z\mathbf{k})$, while $\mathbf{v}$ is the diagonal of the parallelogram defined by $(x\mathbf{i}+z\mathbf{k})$ and $y\mathbf{j}$, that is $\ x\mathbf{i}+z\mathbf{k}+y\mathbf{j}$.
```

As mentioned earlier, you can express every vector as a sequence of three steps\translations: starting from the origin of the frame, we move $x$ units along the x-axis. Then, from that position, we move $y$ units in the y-axis direction, and finally, you move $z$ units in the z-axis direction. This is why we refer to $\mathbf{i}$, $\mathbf{j}$, and $\mathbf{k}$ as basis vectors: any bound vector in a frame can be expressed as a combination of these three unit vectors, with the components of the vector acting as coefficients.

The components of a bound vector represent the coordinates of its arrowhead within the frame. This allows us to uniquely identify all points within a frame using vector notation. However, we still need a way to distinguish between vectors and points, as they are not interchangeable. For vectors, only their direction and magnitude matter, making the point of application irrelevant. On the other hand, points uniquely represent a location and only make sense when bound to the origin of a frame. Subtracting points yields a vector that specifies the movement from one point to another, while adding a point and a vector gives a vector that specifies how to move a point to a different location. However, unlike vectors, adding two points does not have a meaningful interpretation ─ geometrically it's the diagonal of a parallelogram, but this interpretation is not particularly useful or relevant when working with points.

In summary, consider vectors as free vectors and points as bound vectors. When working with a generic vector $\mathbf{v}=(x, y, z)$, it is crucial to determine whether it represents a point or a vector to use it appropriately. The distinction between points and vectors will be further explored in a later tutorial.


##### Gram-Schmidt Orthogonalization

A computer cannot exactly represent all elements in the infinite set of real numbers because it uses a finite number of bits to store values in memory. This means that we must to settle for good approximations. The downside is that, if you need to perform many calculations with approximate values, the outcome could differ significantly from the exact result. For example, a set of vectors $\{\mathbf{v_0},\dots,\mathbf{v_{n-1}}\}$ is called **orthonormal** if they are all unit vectors and orthogonal to each other. However, due to numerical precision issues, we might start off with an orthonormal set that gradually becomes un-orthonormal after some transformations. Fortunately, there are methods available to orthogonalize a set of vectors to restore its orthonormality. While our primary focus will be on the 3D case, it is often helpful to start by examining the simpler 2D case.

Suppose we have an initial set of vectors $\{\mathbf{v_0},\mathbf{v_1}\}$ that is not orthonormal, and we want to orthogonalize it to obtain an orthonormal set $\{\mathbf{w_0},\mathbf{w_1}\}$. To begin the orthogonalization process, we can set $\mathbf{w_0}=\mathbf{v_0}$, as we can always assume one of the vectors in the set is already acceptable. Next, we aim to make $\mathbf{v_1}$ orthogonal to $\mathbf{w_0}$. This can be achieved by subtracting from $\mathbf{v_1}$ its projection onto $\mathbf{w_0}$. By doing so, we obtain a new vector $\mathbf{w_1}$ that is orthogonal to $\mathbf{w_0}$. Indeed, in the following illustration, you can verify that

$$\mathbf{v_1}=\mathbf{w_1}+\text{proj}_{\mathbf{w_0}}(\mathbf{v_1})$$

```{figure} images/01/2D-Gram-Schmidt.png
```

So, we have that

<br>

$$\mathbf{w_1}=\mathbf{v_1}-\text{proj}_{\mathbf{w_0}}(\mathbf{v_1})$$

<br>

where $\ \text{proj}_{\mathbf{w_0}}(\mathbf{v_1})=\displaystyle\frac{\mathbf{v_1}\cdot\mathbf{w_0}}{|\mathbf{w_0}|^2}\mathbf{w_0}$

To prove that $\mathbf{w_0}$ and $\mathbf{w_1}$ are orthogonal to each other, we can first observe that a projection is orthogonal if the direction of projection forms a right angle $(90°)$ with the vector we project onto (see the dashed line in the illustration above). Also, we know that the sum of two vectors is the diagonal of the parallelogram with sides the two vectors. In this case we obtain a rectangle since we just established that an angle of the parallelogram with diagonal $v_1$ is $90°$. So, we verified that $\mathbf{w_0}$ and $\mathbf{w_1}$ are orthogonal: $\mathbf{w_0}\ \bot\ \mathbf{w_1}$.

In the 3D case, we introduce a third vector $\mathbf{v_2}$ that we need to modify in order to make it orthogonal to both $\mathbf{w_0}$ and $\mathbf{w_1}$. As before, we start by setting $\mathbf{w_0}=\mathbf{v_0}$, and we can still use the method of subtracting the projection of $\mathbf{v_1}$ onto $\mathbf{w_0}$ to compute $\mathbf{w_1}$. This is possible because we can consider $\mathbf{v_0}$ and $\mathbf{v_1}$ as lying in the same plane, thereby reducing the problem to the 2D case. To calculate $\mathbf{w_2}$, we proceed similarly. We subtract the projection of $\mathbf{v_2}$ onto $\mathbf{w_0}$ from $\mathbf{v_2}$, obtaining an intermediate vector. Then, we can subtract the projection of $\mathbf{v_2}$ onto $\mathbf{w_1}$ from this intermediate vector. By doing this, we ensure that the resultant vector $\mathbf{w_2}$ is orthogonal to both $\mathbf{w_0}$ and $\mathbf{w_1}$.

$$\mathbf{w_2}=\mathbf{v_2}-\text{proj}_{\mathbf{w_0}}(\mathbf{v_2}) -\text{proj}\_{\mathbf{w_1}}(\mathbf{v_2})$$

Consider the illustration below. If we subtract $\text{proj}\_{\mathbf{w_0}}(\mathbf{v_2})$ from $\mathbf{v_2}$ the resultant vector is orthogonal to $\mathbf{w_0}$ and lies in the YZ-plane. Then, if we subtract $\text{proj}\_{\mathbf{w_1}}(\mathbf{v_2})$ from this intermediate vector, we get $\mathbf{w_2}$, which is orthogonal to both $\mathbf{w_0}$ and $\mathbf{w_1}$.

```{figure} images/01/3D-Gram-Schmidt.png
```

The final step in the process of orthogonalization is to normalize the vectors $\mathbf{w_0}$, $\mathbf{w_1}$, and $\mathbf{w_2}$ to obtain an orthonormal set.


#### Cross product [WIP]

<br>