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


### Basic operations [WIP]

<br>