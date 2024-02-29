# Spaces

The magic of computer graphics lies in the ability to transform and manipulate objects within cartesian coordinate systems, also known as spaces for brevity. Indeed, a fundamental concept that forms the backbone of many graphics pipelines is the transformation of 3D objects to represent them in various spaces in order to bring virtual scene to be shown on the screen.

In [](transformations.md), we showed that to transform a points and vectors we can transform the basis vectors representing the starting frame, so that we can express the coordinates with respect to a new space. Building upon this foundation, it's now interesting to look at the common spaces typically employed in the graphics pipeline and how to go from a space to another as well in order to project the 3D scenes onto a 2D surface before showing the result on the screen. 

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
Throughout this tutorial series, we'll use a left-handed coordinate system where the y-axis points upwards to represent the object space. As explained in [](vectors.md), this is completely arbitrary and you can choose any configuration (z-up or y-up) and handedness that suits your needs.
```

<br>

## World space and World matrix

When the input assembler sends its output to the next stage (the vertex shader), we have vertices in local space that we want to place in a 3D global scene shared by all objects. The space of the global scene is called **world space**, and the transformation to go from local to world space is called **world transformation**. To represent the world space, we will use the same convention as the object space: left-handed system with the y-axis pointing upwards.

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

Given a cube in local space, suppose you want to double its size, rotate it by $45°$ clockwise about the y-axis, and move it at $(10,0,5)$ in the world space. Therefore, we must transform all the vertices of the cube using the following world matrix:

$$
\begin{align*}
\mathbf{W}=\mathbf{SRT}&=\left\lbrack\matrix{2&0&0&0\cr 0&2&0&0\cr 0&0&2&0\cr 0&0&0&1}\right\rbrack\left\lbrack\matrix{\sqrt2/2&0&-\sqrt2/2&0\cr 0&1&0&0\cr \sqrt2/2&0&\sqrt2/2&0\cr 0&0&0&1}\right\rbrack\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr 10&0&5&1}\right\rbrack \\ 
\\
&=\left\lbrack\matrix{\sqrt2&0&-\sqrt2&0\cr 0&2&0&0\cr \sqrt2&0&\sqrt2&0\cr 10&0&5&1}\right\rbrack
\end{align*}
$$

The image below shows a 2D cross-section of the scene, obtained by looking down along the positive y-axis. Note that the first three rows of the $\mathbf{W}$ matrix are the scaled and rotated basis vectors of the local space in world coordinates (that is, whose coordinates are with respect to the world space), while the fourth row is the position (point) of the origin of the translated local space (again, in world coordinates). Based on the information provided in [](matrices.md), we can use $\mathbf{W}$ to transform the vertices of the cube in order to place it in the global scene.

```{figure} images/04/example1.png
```
````

<br>

## View space and View matrix 

Once we apply the world transformation, all objects are in world space. However, we still require a specific viewpoint to observe the 3D scene. This space is typically called **view space**, or **camera space**, and to represent it, we'll adopt a left-handed system similar to the ones used for local and world spaces, where the y-axis points upwards. To transition objects from world space to view space, we need to apply another transformation: the **view transformation**. This involves applying a **view matrix** (denoted by $\mathbf{V}$), which configures the camera's position, orientation, and perspective within the global scene. By applying the view matrix, we effectively convert the coordinates of objects' vertices from world space to view space, establishing the desired viewpoint for our 3D scene.

```{figure} images/04/view-space.png
```

Unlike the world transformation, where each object typically requires a separate transformation, the view transformation usually employs the same view matrix for all objects in the global scene. This is because we generally aim for a consistent viewpoint to observe the entire 3D scene. In other words, it's as if we can consider the entire scene, encompassing all the objects, as a single large object that needs to be transformed from world space to view space.

Now, to build the view matrix, we can start considering the camera as an ordinary object we can place in world space. So, we can use a world matrix $\mathbf{W}_ c$ to put the camera in the global space as the point of view from which to look at the scene.

$$\mathbf{W}_c=\left\lbrack\matrix{f_x&f_y&f_z&0\cr g_x&g_y&g_z&0\cr h_x&h_y&h_z&0\cr t_x&t_y&t_z&1}\right\rbrack$$

$\mathbf{W}_c$ is the matrix that transforms from the local space of the camera to the world space. However, note that the local space of the camera is exactly the view space, the perspective from which we observe the scene. This means that $\mathbf{W}_c$ is the matrix for transitioning from view space to world space. Therefore, we can simply compute the inverse $\mathbf{W}_c^{-1}$ to get the view matrix allowing to go from world space to view space. In this case, computing the inverse is straightforward as $\mathbf{W}_c$ is the composition of a rotation and a translation: $\mathbf{W}_c=\mathbf{RT}$. Indeed, it doesn't make any sense to scale the camera since it's a dummy object; we are only interested in the position and orientation of the view space. And we don't need scaling in the inverse transformation either, as we've already scaled the objects during the world transformations, aiming to maintain their size in world space. Therefore, the first three rows of $\mathbf{W}_c$ represent the rotations of the standard basis vectors of the view space relative to the world space (that is, in world coordinates), while the last row represents the position of the origin of the view space, in world coordinates as well. So, we have

$$\mathbf{V}=\mathbf{W}_c^{-1}=(\mathbf{RT})^{-1}=\mathbf{T}^{-1}\mathbf{R}^{-1}=\mathbf{T}^{-1}\mathbf{R}^{-T}=\left\lbrack\matrix{1&0&0&0\cr 0&1&0&0\cr 0&0&1&0\cr {-t}_x&-t_y&-t_z&1}\right\rbrack\left\lbrack\matrix{f_x&g_x&h_x&0\cr f_y&g_y&h_y&0\cr f_z&g_z&h_z&0\cr 0&0&0&1}\right\rbrack$$

Indeed, remember that the inverse of a rotation matrix is equivalent to its transpose (as explained in [](matrices.md)). Consequently, the view matrix $\mathbf{V}$ to go from world space to view space is

$$\mathbf{V}=\left\lbrack\matrix{f_x&g_x&h_x&0\cr f_y&g_y&h_y&0\cr f_z&g_z&h_z&0\cr -\mathbf{t}\cdot\mathbf{f}&-\mathbf{t}\cdot\mathbf{g}&-\mathbf{t}\cdot\mathbf{h}&1}\right\rbrack$$

It's interesting to note that, since $\mathbf{f}$, $\mathbf{g}$, $\mathbf{h}$ and $\mathbf{t}$ are all in world coordinates, we can compute the view matrix $\mathbf{V}$ from scratch by only using two points in the world space: the origin $\mathbf{O}_v$ of the view space and a target point $\mathbf{T}$ indicating where the camera is aimed. To verify this, let's start by observing that

$$\mathbf{t}=\mathbf{O}_v$$

because both $\mathbf{O}_v$ and $\mathbf{t}$ represent the same geometric entity: the position of the view space in world coordinates.

Now, we need to calculate $\mathbf{f}$, $\mathbf{g}$ and $\mathbf{h}$, which represent the transformations of the standard basis vectors $\mathbf{i}$, $\mathbf{j}$ and $\mathbf{k}$ in the camera's local space. Specifically, $\mathbf{h}$, the transformation of $\mathbf{k}$, points in the direction the camera is aimed (z-axis of the camera space in the image above). Therefore, we can calculate it simply by taking the difference between $\mathbf{T}$ and $\mathbf{O}_v$.

$$\mathbf{h}=\displaystyle\frac{\mathbf{T}-\mathbf{O}_v}{\vert\mathbf{T}-\mathbf{O}_v\vert}$$

Observe that since this is the difference between two points in world coordinates, the result is a vector in world coordinates as well.

To compute $\mathbf{f}$ (transformation of $\mathbf{i}$), we will use $\mathbf{j}$, the unit vector we chose pointing upwards in local, world and camera spaces. This is because we typically limit the vertical rotation of the camera to less than $90°$ around the x-axis (the reasons behind this will be explained in another tutorial). As a result, the angle between $\mathbf{j}$ and $\mathbf{g}$ will be less than $90°$. Similarly, the angle between $\mathbf{j}$ and $\mathbf{h}$ will be less than $180°$, as $\mathbf{g}$ and $\mathbf{h}$ must be orthogonal to each other.

```{figure} images/04/view-matrix-rows.png
```

Therefore, we can calculate $\mathbf{f}$ with the following cross product.

$$\mathbf{f}=\displaystyle\frac{\mathbf{j}\times\mathbf{h}}{\vert\mathbf{j}\times\mathbf{h}\vert}$$

As explained above, the vector $\mathbf{j}$ remains consistent regardless the frame of reference (i.e., it has the same coordinates $(0,1,0)$ in both local and world spaces). Therefore, both $\mathbf{j}$ and $\mathbf{h}$ can be considered as expressed relative to the world space, which we defined as a left-handed system. This implies that the direction of $\mathbf{f}$ will be the one that sees $\mathbf{j}$ rotate clockwise towards $\mathbf{h}$, making $\mathbf{f}$, $\mathbf{j}$, and $\mathbf{h}$ a left-handed system. This is exactly the expected result when transitioning from world space to view space, as we usually don't want to change the handedness of the system we are working with after a transformation.

Finally, to compute $\mathbf{g}$ (transformation of $\mathbf{j}$), we can simply calculate the cross product between $\mathbf{h}$ and $\mathbf{f}$.

$$\mathbf{g}=\mathbf{h}\times\mathbf{f}$$

```{note}
Both $\mathbf{f}$ and $\mathbf{h}$ are unit vectors, so we don't need to normalize the result. Recall what we stated in [](vectors.md): the length of the vector resulting from the cross product $\mathbf{u}\times\mathbf{v}$ is $\vert\mathbf{u}\times\mathbf{v}\vert=\vert\mathbf{u}\vert\vert\mathbf{v}\vert\sin{\theta}$. In this case, we have $\vert\mathbf{h}\vert=\vert\mathbf{f}\vert=1$ and $\sin{90°}=1$.
```

DirectXMath provides the helper function **XMMatrixLookAtLH** to build a view matrix similar to the one we discussed in this section (i.e., for transitioning from world to camera spaces defined as left-handed systems). You need to pass the camera position and target point as arguments to this function, which returns the related view matrix.

```{code-block} cpp
// pos: position (in world coordinates) of the (origin of the) view space.
// target: position (in world coordinates) where we want the camera is aimed at.
// up == j (unit basis vector which points up).
XMVECTOR pos = XMVectorSet(x, y, z, 1.0f);
XMVECTOR target = XMVectorZero();
XMVECTOR up = XMVectorSet(0.0f, 1.0f, 0.0f, 0.0f);
 

// Compute the View matrix.
XMMATRIX V = XMMatrixLookAtLH(pos, target, up);
```
<br>

**XMVectorSet** and **XMVectorZero** are also helper functions. They allow us to initialize an **XMVECTOR** variable using a single SIMD instruction (if SIMD instructions are supported by the CPU). 

```{note}
As explained in [](vectors.md), **XMVECTOR** is an alias for **__m128**, so we should avoid initializing it with a simple assignment or the usual array initialization, because these methods may require multiple instructions, which is inefficient. Instead, **XMVectorSet** and **XMVectorZero** offer a dual implementation (No-Intrinsics and SSE-Intrinsics, as detailed in [](transformations.md)) that allows the CPU to leverage SIMD instructions (if supported) to load four values into a 16-byte aligned **__m128** variable in a single instruction, significantly improving performance.
```

The implementation of the **XMMatrixLookAtLH** function should be relatively straightforward to understand, given the concepts we have discussed in this section and in [](transformations.md).

```{code-block} cpp
inline XMMATRIX XM_CALLCONV XMMatrixLookAtLH
(
    FXMVECTOR EyePosition,
    FXMVECTOR FocusPosition,
    FXMVECTOR UpDirection
) noexcept
{
    XMVECTOR EyeDirection = XMVectorSubtract(FocusPosition, EyePosition);
    return XMMatrixLookToLH(EyePosition, EyeDirection, UpDirection);
}
```

```{code-block} cpp
inline XMMATRIX XM_CALLCONV XMMatrixLookToLH
(
    FXMVECTOR EyePosition,
    FXMVECTOR EyeDirection,
    FXMVECTOR UpDirection
) noexcept
{
    assert(!XMVector3Equal(EyeDirection, XMVectorZero()));
    assert(!XMVector3IsInfinite(EyeDirection));
    assert(!XMVector3Equal(UpDirection, XMVectorZero()));
    assert(!XMVector3IsInfinite(UpDirection));

    XMVECTOR R2 = XMVector3Normalize(EyeDirection);

    XMVECTOR R0 = XMVector3Cross(UpDirection, R2);
    R0 = XMVector3Normalize(R0);

    XMVECTOR R1 = XMVector3Cross(R2, R0);

    XMVECTOR NegEyePosition = XMVectorNegate(EyePosition);

    XMVECTOR D0 = XMVector3Dot(R0, NegEyePosition);
    XMVECTOR D1 = XMVector3Dot(R1, NegEyePosition);
    XMVECTOR D2 = XMVector3Dot(R2, NegEyePosition);

    XMMATRIX M;
    M.r[0] = XMVectorSelect(D0, R0, g_XMSelect1110.v);
    M.r[1] = XMVectorSelect(D1, R1, g_XMSelect1110.v);
    M.r[2] = XMVectorSelect(D2, R2, g_XMSelect1110.v);
    M.r[3] = g_XMIdentityR3.v;

    M = XMMatrixTranspose(M);

    return M;
}
```

**EyePosition**, **FocusPosition** and **UpDirection** are the origin, target and up direction of the camera, expressed in world coordinates.

<br>

## NDC space and Projection matrix 

Once we have all objects in camera space, the next step is to project them onto a plane to obtain a 2D representation of the 3D scene. To achieve this, we can ideally place a plane in front of the camera and trace rays from the camera to each vertex of the mesh, as illustrated in the image below. The intersection between these rays and the plane gives us a 2D representation of the corresponding 3D vertices. Note that if the projection rays are parallel to each other and orthogonal to the projection plane, the camera's position becomes irrelevant.

```{figure} images/04/projection.png
```

In the first case, where the projection rays converge towards a focal point, distant objects appear smaller. This replicates the way human vision works in real life and we commonly refer to this type of projection as perspective.

On the other hand, if the projection rays are parallel to each other, the perspective effect is lost, and the size of objects becomes independent of their distance from the camera. This type of projection is known as orthographic.

To better understand the difference, consider the illustration provided below. It depicts two segments of equal size placed at different distances from the camera. In the perspective projection, the closer segment appears longer when projected onto the projection plane, emphasizing the depth perception effect.

```{figure} images/04/projection2.png
```

Fortunately, the intricacies of the projection process are almost transparent to the programmer, who is primarily responsible for defining the portion of the 3D scene to be projected onto the projection plane. Indeed, in most cases, capturing the entire scene is not necessary or desired. Depending on the type of projection being used, different bounding geometries define the region of interest.

In orthographic projections, the region of interest is represented by a box. This box encompasses the portion of the scene that will be projected onto the 2D plane. While we can use any plane in front of the camera as the projection plane, typically the box face closest to the camera is used as the projection window where the 3D scene is projected.

In perspective projections, the region of interest is defined by a frustum. A frustum is the volume enclosed between two parallel planes that intersect a pyramid. The apex of the pyramid corresponds to the camera's position. The plane closer to the camera is called the near plane, while the farther plane is called the far plane. We can obtain a projection window by intersecting the pyramid between the camera and the near plane with another plane. Alternatively, the upper face of the frustum, the intersection between the near plane and the pyramid, can also be used as the projection window. In computer graphics literature, the terms "near plane" and "far plane" are commonly used to refer to the corresponding windows as well.

```{figure} images/04/frustum.png
```

The illustration below clearly demonstrates the differences between perspective and orthographic projections. In both projections, the green ball lies outside the defined region of interest and therefore is not projected onto the projection window.

In the orthographic projection, the red and yellow balls appear the same size, regardless of their distance from the camera. This is because the projection rays are parallel and do not converge towards a focal point, resulting in a lack of perspective distortion.

On the other hand, in the perspective projection, the red ball appears smaller compared to the yellow ball. This is due to the converging projection rays that mimic the behavior of human vision in real life. As objects move further away from the camera, they appear smaller, resulting in the size difference observed in the perspective projection.

```{figure} images/04/persp-ortho.png
```

To define a frustum (for perspective projections) or a box (for orthographic projections), we need to specify the distances from the camera to the near and far planes. For convenience, we typically define this bounding geometry in view space, where the camera position is located at the origin. Additionally, we need to determine the dimensions of the projection window. With this information, we can construct a projection matrix. This matrix transforms 3D vertices from view space to a space called Normalized Device Coordinates (NDC). 

In perspective projection, the frustum defined in view space becomes a box in NDC space. The origin of this box is located at the center of its front face, which corresponds to the transformed near plane\window. A significant aspect of interest is that the objects contained within the box in NDC space (previously within the frustum\box in view space) will have vertex coordinates falling within the following ranges:

$$
\begin{align*}
-1\le x\le 1 \\ 
\\
-1\le y\le 1 \\
\\
0\le z\le 1
\end{align*}
$$

The illustration below depicts the frustum in view space (left) and the corresponding box in NDC space (right) after a perspective transformation. In DirectX, the NDC space is a left-handed system, with the y-axis that points upwards. The z-axis is always perpendicular to both the front and back faces of the box in NDC space and passes through their centers. While this arrangement also holds in view space, it is not an absolute requirement. Indeed, the z-axis in view space can be non-perpendicular to both the near and far planes, and it may pass through a point other than their centers.

```{figure} images/04/persp-ndc.png
```

Now, you might be wondering what's the point of this transformation. The following illustration shows a 2D representation from the top that explains what happens if you transform a frustum to a box. The objects inside the frustum are transformed accordingly, and the projection rays become parallel to each other. This way, we can orthographically project the mesh vertices onto a projection window (typically the front face of the box in NDC space) to mimic the perspective vision we are used to in real life, where objects like roads seem to converge in the distance, and where near objects appear bigger than distant ones. 

```{figure} images/04/persp-ndc2.png
```

````{note}
Interestingly, once we are in NDC space, there is no actual need for explicit projection onto the window plane. Indeed, as mentioned earlier, in NDC space the projection rays are parallel, and the z-axis is orthogonal to the front face of the NDC box, passing through its center (which is the origin of the NDC space). This means that the x- and y-coordinates of vertices remain constant along the projection rays in NDC space, only the z-coordinate varies. Consequently, the x- and y-coordinates of a vertex in NDC space are identical both inside the NDC box and when projected onto the front face (which lies in the $z=0$ plane of the NDC space). The illustration below provides a top-down view that further demonstrates this concept. It shows that the x-coordinate of a vertex remains constant along the projection ray. The same applies to the y-coordinate if you look at the NDC space sideways.

```{figure} images/04/ndc.png
```
````

Usually, that's all we need to know in order to write applications that renders 3D objects on the screen using a perspective or orthographic effect. However, as graphics programmers, we are expected to know how things work under the hood. In particular, knowing how to build a projection matrix might come in useful in the future.

As stated in the note box above, once we go from view space to NDC space, we implicitly get a 2D representation of 3D vertex positions. So, this transformation is definitely related to the concept of projection. Indeed, the associated matrix is called projection matrix, that can vary depending on the type of projection we are interested in. We will start with a couple of matrices associated with the perspective projection, and then we will show the matrix associated with the orthographic projection.


### Perspective projection [WIP]

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