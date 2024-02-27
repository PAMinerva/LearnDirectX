# Spaces

The magic of computer graphics lies in the ability to transform and manipulate objects within cartesian coordinate systems, also known as spaces for brevity. Indeed, a fundamental concept that forms the backbone of many graphics pipelines is the transformation of 3D objects to represent them in various spaces in order to bring virtual scene to be shown on the screen.

In [](transformations.md), we showed that to transform a points and vectors we can transform the basis vectors representing the starting frame, so that we can express the coordinates with respect to a new space. Building upon this foundation, it’s now interesting to look at the common spaces typically employed in the graphics pipeline and how to go from a space to another as well in order to project the 3D scenes onto a 2D surface before showing the result on the screen. 

From the initial local space where each object is defined, to the all-encompassing world space, and the camera space that offers a unique perspective, each space plays a crucial role in the intricate process of rendering. For a more extensive and detailed presentation on spaces used in computer graphics and transformations to go from one space to another, you may find valuable information in {cite}`MarschnerFundamentals2022`.


## Object space 

The object space, also known as local space, is the frame in which 3D objects\meshes are defined. When creating 3D objects, graphic artists often work in a convenient space that simplifies vertex modeling by providing symmetry with respect to the origin of the coordinate system.

```{figure} images/04/object-space.jpg
```

For instance, consider the modeling of a sphere. It is much easier to place all the vertices at an equal distance from the origin, rather than using a random point as the sphere's center. This intuitive choice is not only practical but can also be mathematically justified.

$$
\begin{align*}
&\text{Equation of the sphere with center in } (0,0,0): \quad x^2+y^2+z^2=r^2 \\ 
\\
&\text{Equation of the sphere with center in } (x_0, y_0, z_0): \quad (x-x_0)^2+(y-y_0)^2+(z-z_0)^2=r^2
\end{align*}
$$

```{note}
The local space is the frame where the vertices of a mesh are defined in the first place. These vertices are often stored in a file on disk and can be loaded into memory to create the vertex buffer, which is subsequently sent to the input assembler. Within this buffer, the vertices remain in their local space representation until the graphics pipeline performs the necessary transformations to convert the 3D objects they represent into a 2D representation to show on the screen.
```

```{note}
Throughout this tutorial series, we’ll use a left-handed coordinate system where the y-axis points upwards to represent the object space. As explained in [](vectors.md), this is completely arbitrary and you can choose any configuration (z-up or y-up) and handedness that suits your needs.
```

<br>

## World space and World matrix

When the input assembler sends its output to the next stage (the vertex shader), we have vertices in local space that we want to place in a 3D global scene shared by all objects. The space of the global scene is called world space, and the transformation to go from local to world space is called world transformation. To represent the world space, we will use the same convention as the object space: left-handed system with the y-axis pointing upwards.

```{figure} images/04/world-space.png
```

As we know, to go from a frame to another, we need to express the basis vectors of the starting frame with respect to the new frame. So, we can build a matrix $\mathbf{W}=\mathbf{SRT}$ (a composition of three trasformations: a scaling, a rotation, and a translation) associated with the world transformation we want to apply to the local frame to get the coordinates of its basis vectors with respect to the world space. Therefore, $\mathbf{W}$ is the matrix to go from local to world space, and that allows us to put an object (or rather, its vertices) in the global scene. For this reason, we call $\mathbf{W}$ the world matrix. 

````{important}
We hardly place every object in the same location of the world space, so $\mathbf{W}$ will likely be different from object to object.

```{figure} images/04/local2world.png
```
````

Thus, we can define $\mathbf{W}$ as 

$$\mathbf{W}=\mathbf{SRT}=\left\lbrack\matrix{f_x&f_y&f_z&0\cr g_x&g_y&g_z&0\cr h_x&h_y&h_z&0\cr t_x&t_y&t_z&1}\right\rbrack$$

where the first three rows of $\mathbf{W}$ are the scaled and rotated basis vectors of the local space with respect to the world space, while the last row is the translation of the origin of the local space (again, with respect to the world space).

````{prf:example}

Given a cube in local space, suppose you want to double its size, rotate it by $45°$ clockwise about the y-axis, and move it at $(10,0,5)$ in the world space. Therefore, we must transform all the vertices of the square with the following world matrix.

$$\mathbf{W}=\mathbf{SRT}=\left\lbrack\matrix{2&0&0&0\cr 0&2&0&0\cr 0&0&2&0\cr 0&0&0&1}\right\rbrack\left\lbrack\matrix{\sqrt2/2&0&-\sqrt2/2&0\cr 0&1&0&0\cr \sqrt2/2&0&\sqrt2/2&0\cr 0&0&0&1}\right\rbrack\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr 10&0&5&1}\right\rbrack=\left\lbrack\matrix{\sqrt2&0&-\sqrt2&0\cr 0&2&0&0\cr \sqrt2&0&\sqrt2&0\cr 10&0&5&1}\right\rbrack$$

As you can see in the following illustration, the first three rows of $\mathbf{W}$ are the scaled and rotated basis vectors of the local space in world coordinates (that is, whose coordinates are with respect to the world space), while the fourth row is the position (point) of the origin of the translated local space (again, in world coordinates). Based on the information provided in [](matrices.md), we can use $\mathbf{W}$ to transform the vertices of the cube in order to place it in the global scene.

```{figure} images/04/example1.png
```
````

<br>

## View space and View matrix [WIP]

<br>




<br>

````{admonition} Support this project
If you found the content of this tutorial somewhat useful or interesting, please consider supporting this project by clicking on the Sponsor button below. Whether a small tip, a one-time donation, or a recurring payment, all contributions are welcome! Thank you!

```{figure} ../../../sponsor.png
:align: center
:target: https://github.com/sponsors/PAMinerva

```
````

<br>

## References
```{bibliography}
:filter: docname in docnames
```
<br>