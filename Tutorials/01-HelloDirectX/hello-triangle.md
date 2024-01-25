# Hello Triangle

<br>

```{figure} images/02/HelloTriangle.png
```

## Introduction

This tutorial builds on the previous one ([](hello-window.md)), so I highly recommend completing it before going through this tutorial if you have not done so already.

Here, we will examine a sample ([D3D12HelloTriangle](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld)) that draws a triangle on the render target. Actually, the code of this sample hardly changes compared to that of **D3D12HelloWindow**. However, there is still a lot to explain. The reason is that this time we will make use of the rendering pipeline to draw something on the render target.

If you are familiar with video games, you know that the image you see on the screen is only a 2D representation of 3D geometries composed of polygons (most of the time triangles) defined by vertex locations in 3D space.

```{figure} images/02/polys.jpg
```

It can be stated that the whole art of computer graphics boils down to creating a 2D representation from a 3D scene. Indeed, this is the result of processing 3D geometries through a rendering pipeline, which will be briefly discussed in the next section.

In {numref}`3d-2d`, you can see a 3D scene projected onto a 2D rectangle\window lying on a plane in front of a special viewpoint called the camera. You can think of a 2D projection window as the film of a camera (or the retina of the human eye) that captures the light and allows the creation of a 2D representation from a 3D scene. Eventually, this 2D representation is mapped to the render target, which, in turn, is mapped to the window's client area.

```{figure} images/02/3d-scene-2d-repres.png
---
name: 3d-2d
---
Projection of a 3D scene onto a 2D plane
```

You can easily guess that only a particular region of the 3D scene should be captured by the camera (that is, we generally won't capture the whole 3D scene). The extent of the region captured can be controlled in a similar way to how we set the field of view (FOV) of the camera lens when we want to take a picture.  However, in computer graphics, the film is typically positioned in front of the camera lens, not behind.

Now, we need to define a couple of terms that we will use throughout the rest of this tutorial:

**Monitors** are raster displays, which means the screen can be seen as a two-dimensional grid of small dots called pixels. Each pixel displays a color that is independent of other pixels. When we render a triangle on the screen, we don't actually render a triangle as one entity. Instead, we illuminate the group of pixels that are covered by the triangle's area.

```{figure} images/02/raster-traingle.png
```

**Textures** are typically 2D digital images composed of small dots called texels that contain various information (e.g., color, distance, direction, etc.). We can also use textures as render targets, essentially treating them like a canvas where we can draw by setting colors to texels. Subsequently, the final image representing a frame, can be mapped to the window's client area to display the result to the user.

```{note}
A physical pixel is a square with an area, but its coordinates refer to a specific point (usually, the top-left corner of the pixel). However, GPU calculations and sampling operations often occur at the center of the pixels. When we describe a monitor as a 2D grid of dots, we are actually referring to rows and columns intersecting at pixel coordinates. The same considerations also apply to textures, which can be seen as grids of texels (texture elements) with an area, whose coordinates refer to a specific point within the texel's area. We will revisit this concept in an upcoming tutorial.
```

The next section will provide a high-level overview of the graphics pipeline, explaining its role in rendering graphics to a render target.

[WIP]


## References
```{bibliography}
:filter: docname in docnames
```