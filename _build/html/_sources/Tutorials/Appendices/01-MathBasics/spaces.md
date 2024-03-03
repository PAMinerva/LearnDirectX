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
\begin{flalign}
\text{Equation of the sphere with center in } (0,0,0):\quad &x^2+y^2+z^2=r^2 \\ 
\\
\text{Equation of the sphere with center in } (x_0, y_0, z_0):\quad &(x-x_0)^2+(y-y_0)^2+(z-z_0)^2=r^2
\end{flalign}
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

Now, to build the view matrix, we can start considering the camera as an ordinary object we can place in world space. So, we can use a world matrix $\mathbf{W}_c$ to put the camera in the global space as the point of view from which to look at the scene.

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

(spaces-ndc-proj-label)=
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

In perspective projections, the region of interest is defined by a frustum. A frustum is the volume enclosed between two parallel planes that intersect a pyramid. The apex of the pyramid corresponds to the camera's position. The plane closer to the camera is called the near plane, while the farther plane is called the far plane. We can obtain a projection window by intersecting the pyramid between the camera and the near plane, with another plane parallel to the near one. Alternatively, the upper face of the frustum, the intersection between the near plane and the pyramid, can also be used as the projection window. In computer graphics literature, the terms "near plane" and "far plane" are commonly used to refer to the corresponding windows as well.

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


### Perspective projection 

While DirectX offers convenient helper functions for constructing projection matrices, in this section we will explore the process of manually creating a couple of projection matrices based on frustum information. Our first objective is to derive NDC coordinates from view coordinates. Then, we will attempt to express the resulting equations in matrix form, with the goal of finding a projection matrix to go from the view space to the NDC space. Consider the following illustration.

```{figure} images/04/persp-proj.png
---
name: spaces-persp-proj
---
Frustum in view space
```

To construct a projection matrix, we must first define a frustum (in view space) that provides the necessary information. Regarding the projection window, as explained in [](spaces-ndc-proj-label), we can opt for the intersection between the pyramid formed by the camera and the near plane, with any plane parallel to the near one. For our purposes, let's conveniently choose a plane at a distance $d$ along the z-axis from the camera (located at the origin $\mathbf{O}_v$ of the view space) so that the height of the projection window is $2$.

The angle $\theta$, known as the vertical FOV (field of view), allows us to control the vertical extent of the visible scene in view space. Notably, by setting $\theta$ as the angle between the top and bottom sides of the frustum, we implicitly establish the distance $d$, given our earlier requirement that the height of the projection window be $2$. On the other hand, the angle $\phi$ controls the horizontal FOV. This means we can zoom-in by simply decreasing $\theta$ and\or $\phi$, as this reduces the visible region, which results in fewer objects being projected onto the projection window. Conversely, if we increase $\theta$ and\or $\phi$ we have a zoom-out effect because more objects are projected onto the projection window.

However, the horizontal FOV $\phi$ is not typically used directly to control the width of the projection window. Instead, we set the aspect ratio $r$ of the window. This approach is crucial to prevent distortion when mapping the projection window onto the render target, which ultimately gets mapped to the window's client area. Hence, we define $r=w/h=w/2$, where $w$ and $h$ represent the width and length of the render target, respectively. Consequently, the width of the projection window is $w=2r$. As depicted in the illustration above, the horizontal FOV $\phi$ is dependent on both the aspect ratio $r$ and the distance $d$ of the projection window (which, in turn, depends on the vertical FOV $\theta$). 

Observe that the z-axis is orthogonal to the projection window and passes through its center, dividing the height of the projection window into two parts of unit lengths. Also, the near and far planes are located at a distance of $n$ and $f$ units from the camera. This represents the most commonly used form of frustum, although it is just a specific case. We will explore a more general case later in this section.

Since the z-axis is orthogonal to the projection window and passes through its center, any 3D vertex projected onto its surface will have the y-coordinate already in NDC space (i.e., within the range $[-1, 1]$). You can verify it in the illustration above, where the perspective projection $y_w$ of the y-coordinate $y_v$ of a 3D vertex in view space is always within the range $[-1, 1]$. On the other hand, the perspective projection $x_w$ of the x-coordinate $x_v$ of the same vertex in view space needs to be scaled by $1/r$ to fit within the same range. As for the z-coordinate, it requires a separate discussion, which we will delve into shortly.

```{figure} images/04/persp-proj2.png
```

Let's start by examining $y_w$, which represents the perspective projection of the y-coordinate of a 3D vertex $\mathbf{v}_v=(x_v, y_v, z_v, w_v)$ in view coordinates. The projection window lies in the plane $z=d$. As mentioned at the begin of the section, we want to find a formula to derive NDC coordinates from view coordinates. To achieve this, let's consider the triangles $\triangle_1=(\mathbf{O}_v, d, y_w)$ and $\triangle_2=(\mathbf{O}_v, z_v, y_v)$. These triangles are similar because they share an angle (the one at $\mathbf{O}_v$), and both have a right angle. So, we have

$$
\begin{align}
\frac{y_w}{d}&=\frac{y_v}{z_v} \\ 
\\
y_{ndc} &= y_w = \frac{dy_v}{z_v}\quad\quad\quad\quad\tag{1}\label{eq:ASpaces1}
\end{align}
$$

Also, we know that $d$ depends on the vertical FOV $\theta$. Indeed, you can check from image {numref}`spaces-persp-proj` that 

$$
\begin{align*}
\frac{1}{d}&=\tan{\left({\frac{\theta}{2}}\right)} \\ 
\\
d&=\frac{1}{\tan{(\theta/2)}}=\cot{\left(\frac{\theta}{2}\right)}\quad\quad\quad\quad\tag{1b}\label{eq:ASpaces1b}
\end{align*}
$$

If you want to compute the horizontal FOV $\phi$, we have that

$$
\begin{align*}
tan{(\phi/2)}&=r/d=r/cot{(\theta/2)}=r\ tan{(\theta/2)} \\ 
\\
\phi&=2\ {tan}^{-1}{(r\ tan{(\theta/2)})}\quad\quad\quad\quad\tag{1c}\label{eq:ASpaces1c}
\end{align*}
$$

As for $x_w$, we have a similar calculation. However, we need to consider the width of the projection window and the final scaling by $1/r$ to derive the related NDC coordinate.

$$
\begin{align*}
\frac{x_w}{d}&=\frac{x_v}{z_v} \\ 
\\
x_w&=\frac{dx_v}{z_v} \\
\\
x_{ndc}&=\frac{x_w}{r}=\frac{dx_v}{rz_v}\quad\quad\quad\quad\tag{2}\label{eq:ASpaces2}
\end{align*}
$$

Observe that a vertex in view space $\mathbf{v}_v=(x_v, y_v, z_v, w_v)$ is inside the frustum if and only if

$$
\begin{align*}
-r\ \le\ x_w\ \le\ r \\ 
\\
-1\ \le\ y_w\ \le\ 1 \\
\\
n\ \le\ z_v\ \le\ f
\end{align*}
$$

where $x_w$ and $y_w$ are the first two coordinates of the projection of $\mathbf{v}_v$ onto the projection window. As for the other two NDC coordinates, we have

$$
\begin{align*}
z_{ndc}&=z_{ndc}\quad\quad\quad\quad\tag{3}\label{eq:ASpaces3} \\ 
\\
w_{ndc}&=w_v=1\quad\quad\quad\quad\tag{4}\label{eq:ASpaces4}
\end{align*}
$$

As we know, a vertex position is a point, so the w-coordinate is always 1 regardless of the coordinate space. As for $z_{ndc}$, there are additional considerations to take into account before deriving this NDC coordinate from the view one, so we will temporarily write a dummy $z_{ndc}=z_{ndc}$.

However, before deriving $z_{ndc}$ from $z_v$, we can draw our first, unfortunate, conclusion: we cannot express in matrix form the transformation from view space to NDC space using equations $\eqref{eq:ASpaces1}$ through $\eqref{eq:ASpaces4}$. The reason is that a matrix form can be used if we have a linear combination of the rows of the matrix with the components of the vector as coefficients, as explained in [](matrices.md) and [](transformations.md). The presence of the $z_v$ term in the denominator of $x_{ndc}$ and $y_{ndc}$ (equations $\eqref{eq:ASpaces1}$ and $\eqref{eq:ASpaces2}$) prevents us from directly representing the transformation as a matrix multiplication. Indeed, there’s no way to multiply a vector by a matrix and have a vector component appearing in the denominator of any coefficient within the linear combination of the matrix's rows. So, we need to come up with something else to get a matrix form anyway.

Observe that if we multiply the NDC coordinates by $z_v$ (equations $\eqref{eq:ASpaces1}$ through $\eqref{eq:ASpaces4}$) we can derive a matrix form to transform a vertex from view space to a temporary space that is similar to NDC space, except for a division by $z_v$. The trick is to place $z_v$ in the w-component of the temporary coordinates and let the rasterizer perform the division by $z_v$ for us. This operation is called perspective division because it is related to the perspective projection. Observe that we are using the last coordinate to hold a value that divides the other components, so this temporary space is an homogeneous space, as explained in [](vectors.md). This homogeneous space is usually referred to as clip space (and its coordinates as clip coordinates) because it is a convenient space for discarding primitives outside the frustum and clipping primitives that intersect the frustum’s boundary (more on this shortly). Well, it turns out that clip coordinates are exactly what the rasterizer expects before performing perspective division, so we can use this trick to find a matrix form that transforms view coordinates into clip coordinates.

```{note}
The rasterizer expects to receive primitives with vertices in clip coordinates as input. Therefore, the last stage before the rasterizer must output vertices in clip space. Typically, if no optional stage is enabled, the last stage before the rasterizer is the vertex shader. Otherwise, it can be one between geometry shader and domain shader.
```

With the perspective division automatically performed by the rasterizer, we are able to transform the coordinates of a vertex from clip to NDC space. Now, we need to find a matrix form to go from view space to clip space. To do this, we must first multiply equations equations $\eqref{eq:ASpaces1}$ through $\eqref{eq:ASpaces4}$ by $z_v$ to obtain the clip coordinates $\mathbf{v}_c$, that represents the vertex in clip space.

$$
\begin{align*}
x_c&=\frac{d\ x_v}{r} \cr \cr
y_c&=d\ y_v \cr \cr
z_c&=z_{ndc}\ z_v \cr \cr
w_c&=z_v
\end{align*}
$$

Remember that we still need to derive $z_{ndc}$ from the view coordinates of the vertex. Meanwhile, we can write the above equations in the following matrix form as follows:

$$
\begin{align*}
\mathbf{v}_c=\left\lbrack\matrix{x_v&y_v&z_v&w_v}\right\rbrack\left\lbrack\matrix{d/r&0&0&0\cr 0&d&0&0\cr 0&0&z_ {ndc}&1\cr 0&0&0&0}\right\rbrack \quad\quad\quad\quad\tag{5}\label{eq:ASpaces5} \\
\end{align*}
$$

Then, to get the NDC coordinates, we simply need to divide all the components of $\mathbf{v}_c$ by $z_v$ (operation automatically performed by the rasterizer; we will simply enjoy the result in the pixel shader).

We can now focus on deriving a formula for $z_{ndc}$ starting from $z_v$. Actually, we simply need to re-map $z_v$ from $[n, f]$ (the range defined by the near and far planes of the frustum in view space) to $[0, 1]$ (the range defined by front and back faces of the corresponding box in NDC space). That is, we want the normalized distance between the vertex and the projection window (front face of the NDC box). 

```{note}
As already mentioned several times in this section, we can intersect any plane with the pyramid between the camera and the near plane to obtain the projection window. The result represents the same projection window but at different distances from the camera. This difference in distance does not affect the x- and y-coordinates, as previously explained. However, it does impact the z-coordinate, which requires to be handled separately, as discussed in the current explanation.
```

Observe that $n$ in view space is mapped to $0$ in NDC space, while $f$ is mapped to $1$. Therefore, we need a scaling $S$ to resize the range $[n, f]$ from $(f-n)$ to $1$ (the size of the range $[0, 1]$), plus a translation $T$ to shift the scaled range so that it covers exactly the values in $[0, 1]$. Therefore, the generic equation is

$$z_c=Sz_v+T$$

```{note}
The equation above uses $z_c$ (instead of $z_{ndc}$) to denote a clip coordinate. This is because the matrix in equation $\eqref{eq:ASpaces5}$ transforms from view space to clip space, not directly to NDC space. However, this does not alter the observations made above: we still require a scaling operation followed by a translation to resize and remap the range of the z-coordinate, even in clip space. Indeed, the difference between clip and NDC coordinates can be seen a straightforward, additional scaling operation.
```

Consequently, the matrix in equation $\eqref{eq:ASpaces5}$ becomes

$$\left\lbrack\matrix{d/r&0&0&0\cr 0&d&0&0\cr 0&0&S&1\cr 0&0&T&0}\right\rbrack \tag{6}\label{eq:ASpaces6}$$

because the last two entry in the third column are the only ones that can scale and translate the third coordinate of $\mathbf{v}_v$, that is $z_v$. To find $S$ and $T$, we can multiply the matrix above by a couple of vertices in view space which we already know the results in NDC space. For example, we know that for a vertex in view space that lies in the near plane we have $z_{ndc}=0$ (since $z_v=n$, and we know that $n$ maps to $0$). So, if we multiply the vertex $\mathbf{v}_v=(0, 0, n, 1)$ (which lies in the near plane) by the matrix $\eqref{eq:ASpaces6}$, we have

$$\mathbf{v}_c=\left\lbrack\matrix{0&0&n&1}\right\rbrack\left\lbrack\matrix{d/r&0&0&0\cr 0&d&0&0\cr 0&0&S&1\cr 0&0&T&0}\right\rbrack=\left\lbrack\matrix{0\cr 0\cr Sn+T\cr n}\right\rbrack^T$$

The coordinates of $\mathbf{v}_c$ are in clip space, and after the perspective division (by $w_c=n$) we have

$$\displaystyle z_{ndc}=S+\frac{T}{n}$$

However, in this case we know that $z_{ndc}=0$, so we have that

$$
\begin{align*}
0&=S+\frac{T}{n} \\
\\
T&=-Sn\quad\quad\quad\quad\tag{7}\label{eq:ASpaces7}
\end{align*}
$$

We also know that for a vertex in view space that lies in the far plane we have $z_{ndc}=1$ (since $z_v=f$, and we know that $f$ maps to $1$). Therefore, if we multiply the vertex $\mathbf{v}_v=(0,0,f,1)$ (which lies in the far plane) by the matrix $\eqref{eq:ASpaces6}$, we have

$$\mathbf{v}_c=\left\lbrack\matrix{0&0&f&1}\right\rbrack\left\lbrack\matrix{d/r&0&0&0\cr 0&d&0&0\cr 0&0&S&1\cr 0&0&-Sn&0}\right\rbrack=\left\lbrack\matrix{0\cr 0\cr Sf-Sn\cr f}\right\rbrack^T$$

where we used $T=-Sn$. After the perspective division (by $w_c=f$) we have

$$\displaystyle z_{ndc}=S-S\ \frac{n}{f}=S\left(1-\frac{n}{f}\right)$$

However, in this case we know that $z_{ndc}=1$ so,

$$
\begin{align*}
1&=S\left(1-\frac{n}{f}\right) \\
\\
S&=\frac{1}{1-\displaystyle\frac{n}{f}}=\frac{1}{\displaystyle\frac{f-n}{f}}=\frac{f}{f-n}
\end{align*}
$$

Substituting this into equation $\eqref{eq:ASpaces7}$ we have

$$ T=-\left(\frac{f}{f-n}\right)n=-\frac{fn}{f-n} $$

We just found the values of $S$ and $T$, so that we can write the z-coordinate both in clip space and in NDC space, after the perspective division, as follows:

$$
\begin{align*}
\displaystyle z_c&=\frac{f}{f-n}z_v-\frac{nf}{(f-n)}\quad\quad\quad\quad\tag{8}\label{eq:ASpaces8} \\
\\
\displaystyle z_{ndc}&=\frac{f}{f-n}-\frac{nf}{(f-n)z_v}\quad\quad\quad\quad\tag{9}\label{eq:ASpaces9}
\end{align*}
$$

Matrix $\eqref{eq:ASpaces6}$ now only depends on the distances of the near and far planes from the camera. This means that we can build this matrix by only using the frustum information at our disposal as follows:

$$\mathbf{P}=\left\lbrack\matrix{d/r\ &0&0&0\cr 0&d&0&0\cr 0&0&\frac{f}{f-n}&1\cr 0&0&-\frac{nf}{f-n}&0}\right\rbrack \tag{10}\label{eq:ASpaces10}$$

Matrix $\eqref{eq:ASpaces10}$ can be used to transform vertex positions from view space to homogeneous clip space.

Although, that’s not what we wanted to find at the start of this section (the matrix to go from view to NDC space). However, since we get the perspective division for free during the rasterizer stage, we can actually consider $\mathbf{P}$ as the perspective projection matrix to go from the view space to NDC space.


#### General case

We built the perspective projection matrix $\eqref{eq:ASpaces10}$ with the assumption that the z-axis goes through the center of the projection window. However, in a more general case, we have a scenario similar to the one shown in the illustration below.

```{figure} images/04/persp-proj3.png
```

Deriving a perspective projection matrix for the general case won't be overly difficult given our exploration of the specific case in the previuos section. Indeed, after projecting the 3D vertices onto the projection window, we need to translate the projection window so that the z-axis goes through its center again, bringing us back to the specific case. However, before proceeding, some initial observations are necessary.

- In the general case, the frustum is not necessarily symmetrical with respect to the z-axis, so we can’t use the vertical FOV and aspect ratio to define the size of the projection window. Instead, we need to set its width and height by specifying the view coordinates of its top, bottom, left, and right sides.

- We will project 3D vertices onto the projection window that lies on the near plane (meaning $d=n$). This isn’t really a limitation because we can project onto any projection window between the camera (exclusive) and near plane (inclusive).

Thus, in the general case, a vertex $\mathbf{v}_v=(x_v, y_v, z_v)$ in view space is inside the frustum if and only if

$$
\begin{align*}
l\ \le\ x_w\ \le\ r \\
\\
b\ \le\ y_w\ \le\ t \\
\\
n\ \le\ z_v\ \le\ f
\end{align*}
$$

where $r, l, t$ and $b$ are the view coordinates of the right, left, top and bottom sides of the projection window that lies on the near plane.

Therefore, we need to translate the first two coordinates of $\mathbf{v}_w$ (projection of $\mathbf{v}_v$) so that the z-axis passes through the center of the projection window.  As mentioned earlier, this brings the problem back to the specific case we have already examined.

$$
\begin{align*}
x_w&=x_w-\frac{r+l}{2} \quad\quad\quad\quad\tag{11}\label{eq:ASpaces11} \\
\\
y_w&=y_w-\frac{t+b}{2} \quad\quad\quad\quad\tag{12}\label{eq:ASpaces12}
\end{align*}
$$

Observe that we used the mid-point formula to subtract the x- and y- center coordinates of the projection window from $x_w$ and $y_w$, respectively. Now, to get the NDC coordinates, we need to scale their ranges. That is, since $x_w$ is in the range $[l, r]$ and $y_w$ is in the range $[b,t]$, we must re-map both to the range $[-1, 1]$. If we multiply $x_w$ (as expressed in equation $\eqref{eq:ASpaces11}$) by $(t-b)^{-1}$ and $y_w$ (as expressed in equation $\eqref{eq:ASpaces12}$) by $(r-l)^{-1}$, then both ranges will be in $[-0.5, 0.5]$. At that point, we only need to multiply by 2 to scale both ranges to $[-1, 1]$. These transformations are reflected in the following equations:

$$
\begin{align*}
x_{ndc}&=\frac{2\ x_w}{r-l}-\frac{2(r+l)}{2(r-l)} \quad\quad\quad\quad\tag{13}\label{eq:ASpaces13} \\
\\
y_{ndc}&=\frac{2\ y_w}{t-b}-\frac{2(t+b)}{2(t-b)} \quad\quad\quad\quad\tag{14}\label{eq:ASpaces14}
\end{align*}
$$

Now that we are back to the specific case, we can substitute equation $\eqref{eq:ASpaces1}$ into equation $\eqref{eq:ASpaces14}$, keeping in mind that we now have $d=n$.

$$
\begin{align*}
y_{ndc}&=\frac{2(ny_v/z_v)}{t-b}-\frac{2(t+b)}{2(t-b)} \\
\\
&=\frac{2n(y_v/z_v)}{t-b}-\frac{(t+b)(z_v/z_v)}{(t-b)} \\
\\
&=\frac{2ny_v}{t-b}\cdot\frac{1}{z_v}-\frac{z_v(t+b)}{(t-b)}\cdot\frac{1}{z_v} \\
\\
&=\frac{1}{z_v}\left(\frac{2n}{t-b}y_v-\frac{t+b}{t-b}z_v\right)\quad\quad\quad\quad\tag{15}\label{eq:ASpaces15}
\end{align*}
$$

Similarly, we can substitute equation $\eqref{eq:ASpaces2}$ (in particular, the second one of the three equations) into equation $\eqref{eq:ASpaces13}$.

$$
\begin{align*}
x_{ndc}&=\frac{2(nx_v/z_v)}{r-l}-\frac{2(r+l)}{2(r-l)} \\
\\
&=\frac{2n(x_v/z_v)}{r-l}-\frac{(r+l)(z_v/z_v)}{(r-l)} \\
\\
&=\frac{2nx_v}{r-l}\cdot\frac{1}{z_v}-\frac{z_v(r+l)}{(r-l)}\cdot\frac{1}{z_v} \\
\\
&=\frac{1}{z_v}\left(\frac{2n}{r-l}x_v-\frac{r+l}{r-l}z_v\right)\quad\quad\quad\quad\tag{16}\label{eq:ASpaces16}
\end{align*}
$$

With equations $\eqref{eq:ASpaces15}$ and $\eqref{eq:ASpaces16}$, we have found a way to calculate the first two NDC coordinates from the corresponding view coordinates. As for $z_{ndc}$, the equation remains unchanged from the one we derived in $\eqref{eq:ASpaces8}$ for the specific case because the solution still involves to map the range $[n,f]$ to $[0,1]$.

If we omit the perspective division $(1/z_v)$ in $\eqref{eq:ASpaces15}$ and $\eqref{eq:ASpaces16}$, we can express the clip coordinates as a linear combination of the view coordinates. This means the perspective projection matrix $\eqref{eq:ASpaces10}$ becomes

$$\mathbf{P}=\left\lbrack\matrix{\frac{2n}{r-l}&0&0&0\cr 0&\frac{2n}{t-b}&0&0\cr -\frac{r+l}{r-l}&-\frac{t+b}{t-b}&\frac{f}{f-n}&1\cr 0&0&-\frac{nf}{f-n}&0}\right\rbrack \tag{17}\label{eq:ASpaces17}$$


#### Perspective division and clipping

After the perspective division by the w-component, the vertices inside the NDC box are the ones with NDC coordinates falling within the following ranges

$$
\begin{align*}
-1\le x/w\le 1 \\ 
\\
-1\le y/w\le 1 \\
\\
0\le z/w\le 1
\end{align*}
$$

This means that the vertices inside the frustum were the ones with homogeneous coordinates falling within the following ranges

$$
\begin{align*}
-w\le x\le w \\ 
\\
-w\le y\le w \\
\\
 0\le z\le w
\end{align*}
$$

That is, the vertices inside the frustum are the ones bounded by the following homogeneous planes (that is, 4D planes expressed in clip coordinates). 

$$
\begin{flalign}
\text{Left: }\quad  &w = -x \\ 
\\
\text{Right: }\quad &w = x \\
\\
\text{Bottom: }\quad  &w = -y \\ 
\\
\text{Top: }\quad  &w = y \\ 
\\
\text{Near: }\quad  &w = 0 \\ 
\\
\text{Far: }\quad  &w = z
\end{flalign}
$$

The following illustration shows a 2D representation of the frustum in the homogeneous xw-plane.

```{figure} images/04/persp-div.png
```

If $w\ne 0$, we know that after the perspective division we always have $w=1$. In this 2D case, the perspective division is essentially a projection onto the line $w=1$. This means that a 2D object is projected to reduce its dimension to 1D. The same applies to objects in clip space: the perspective division projects object in a 4D space (the clip space) onto a 3D Cartesian space (the NDC space).

We have $w=0$ if a vertex lies in the near plane. Indeed, in that case we have $w=z_v=0$, which is the value the rasterizer uses to divide the other coordinates. To avoid divide-by-zero exceptions, as well as processing objects outside the visible region for nothing, the rasterizer clips primitives intersecting the planes of the frustum before executing the perspective division. Clipping is automatically performed by the rasterizer, so we won't cover the implementation of an efficient clipping algorithm here. However, typically, a rasterizer simply clips primitives intersecting the near plane before the perspective division (in clip space), and discard non-visible pixels after the perspective division.

```{figure} images/04/clipping.png
```

As you can see in the image above, a clipped primitive might no longer be a triangle. Therefore, the rasterizer also needs to triangulate clipped primitives, and re-inserts them in the pipeline.


#### Depth buffer precision

Whatever perspective projection matrix you decide to use (either $\eqref{eq:ASpaces10}$ or $\eqref{eq:ASpaces15}$), after the perspective division we have

$$\displaystyle z_{ndc}=\frac{f}{f-n}-\frac{nf}{(f-n)z_v}$$

If you set $n$ and $f$, this equation  represents a strictly increasing function of $z_v$, with $z_v=1$ acting as a horizontal asymptote. Mathematically, this implies that for every $z_v$ value, there exists a unique corresponding $z_{ndc}$. However, when working with computers, this property cannot be relied upon due to the finite precision of floating-point representations. Moreover, the density of representable floating-point values is higher near zero and decreases as values move further away.

The following graph shows what happens if you set $n=1$ and $f=100$. Approximately $80%$ of the NDC values in the range $[0, 1]$ are available for less than $5%$ of the view values in the range $[n,f]$. Consequently, the remaining $95%$ of the view values must share the remaining $20%$ of the NDC values. As a result, it is likely that different view values close to $100$ cannot be represented by unique NDC values. In other words, different $z_v$ values can produce the same $z_{ndc}$ if the corresponding vertices are close to each other and far from the camera.

```{figure} images/04/depth-precision.png
```

This can represent a serious problem because if a far object A is in front of another object B, but A is rendered after B, then A could be considered at the same distance as B with respect to the camera, and discarded from the pipeline if the depth test is enabled. We will delve into depth testing in a subsequent tutorial.

To mitigate the problem, we can set $n$ and $f$ to make the near and far planes as close as possible. In the illustration above, you can see what happens if we set $n=10$ and $f=100$. The function grows slower, so we have a more even distribution of NDC values. Another mitigation technique is to redesign the projection matrix so that the clip space depths $z_c$ are still mapped to $[0,1]$, but in reverse order. That is, after the perspective division we have that $z_{ndc}$ is a strictly decreasing function, where the NDC values are much more evenly distributed between near and far planes. Additional details on the inverse projection matrix will be provided in a subsequent tutorial.


### Orthographic projection [WIP]

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