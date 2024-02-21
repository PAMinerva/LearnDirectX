# Transformations

In graphics applications, it is common to apply transformations such as scaling, rotation, and translation to objects before displaying them on the screen. For example, let's consider a scenario where you want to move an object to a different position in the scene. In order to achieve this, you need to move all the vertices of the object by applying a transformation on them. Matrices are mathematical entities that represent transformations. This means that if we treat vertex positions as vectors in a 3D space, we can multiply them by a matrix to change their position, orientation and magnitude in the scene. In the example given, we would perform a matrix multiplication between each vertex position $\mathbf{v}=(x,y,z)$ stored in the vertex buffer and a translation matrix $T$ in order to change its coordinates.

$$T(\mathbf{v})=(x\textrm', y\textrm', z\textrm')=\mathbf{w}$$

Here, $\mathbf{w}$ represents the new position of the vertex $\mathbf{v}$ after the transformation $T$. <br>
Now, it's interesting how we can visualize it in two different ways. Obviously, we can move (translate) $\mathbf{v}$ until its coordinate match those of $\mathbf{w}$. Alternatively, we can translate the entire frame to achieve the same result. In [](vectors.md), we established that points (and therefore positions) are vectors bound to the origin of a frame, which can be described by the coordinates of the corresponding arrowheads. In the illustration below, on the left side, we translate the arrowhead of $\mathbf{v}$ by changing its coordinates, resulting in the vector\position $\mathbf{w}$. On the right side of the illustration, we apply the same translation to the origin of the frame. As you can observe, the coordinates of $\mathbf{v}$ with respect to the transformed frame A remain the same because the vector shares the same fate (transformation) as its frame of reference. However, now the original frame B "see" $\mathbf{v}$ as being applied to the origin of frame A. Consequently, the coordinates of $\mathbf{v}$ with respect to the original frame B are the same as those of $\mathbf{w}$. Thus, we can consider the transformed frame A as the starting frame, while the original frame B as the new frame of reference.

```{figure} images/03/transformation.png
```

The same concept applies when rotating or scaling a vertex position. As we will explore in this tutorial, vector transformations and changes of coordinate systems are mathematically equivalent. In other words, transforming an object is equivalent to transforming its frame of reference, and vice versa.

<br>

## Linear transformations [WIP]

<br>