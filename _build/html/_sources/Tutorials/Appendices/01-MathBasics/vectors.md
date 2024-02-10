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

```{note}
A single point can have multiple polar coordinate representations due to the periodicity of angles. For example, $(r,\theta)$ and $(r,\theta + 2\pi)$ represent the same point. Be aware of this when working with polar, cylindrical or spherical coordinates.
```

```{figure} images/01/polar-coord.png
```

### Cylindrical and spherical coordinate systems [WIP]