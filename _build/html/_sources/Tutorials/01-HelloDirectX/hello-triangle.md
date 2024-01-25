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

In the figure below, you can see a 3D scene projected onto a 2D rectangle\window lying on a plane in front of a special viewpoint called the camera. You can think of a 2D projection window as the film of a camera (or the retina of the human eye) that captures the light and allows the creation of a 2D representation from a 3D scene. Eventually, this 2D representation is mapped to the render target, which, in turn, is mapped to the window's client area.

```{figure} images/02/3d-scene-2d-repres.png
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

<br>

## Direct3D rendering pipeline overview

GPUs are equipped with thousands of programmable cores, which means that they can execute programs in the form of instructions provided by a programmer, much like how CPU cores run C++ applications. As illustrated below, CPUs typically have fewer cores than GPUs. However, GPU cores are smaller and more specialized in processing tasks that can be divided up and processed across many cores. In particular, GPUs use parallelism to execute the same instructions on multiple cores simultaneously, resulting in high-speed processing of vast amounts of data. On the other hand, CPUs excel at executing many sequential instructions on each core to execute multiple tasks on small data really fast.

```{figure} images/02/CPU-vs-GPU.png
CPU (on the left) versus GPU (on the right)
```

GPUs that support Direct3D can execute programs on their cores to perform the work of various stages composing a pipeline. Direct3D provides different pipeline types, but we will focus on the rendering pipeline (also called the graphics pipeline) for now. A rendering pipeline defines the steps needed to draw something on the render target. In particular, the following image illustrates the stages of the rendering pipeline used to draw on a render target with the Direct3D API.

```{figure} images/02/rendering-pipeline.png
```

Programmable stages can execute programs (often called shader programs, or simply shaders, with the GPU cores executing them often referred to as shader cores) written by programmers. Configurable stages (the rectangles with gear wheels in the image above) can't be programmed: they always execute the same code, but you can still configure their state. This means you can't specify what a configurable stage can do, but you can still specify how it performs its task. For this reason, they are often called fixed-function stages.

Data flows from input to output through each of the configurable or programmable stages. In other words, each stage consumes the input from the previous stage and provides its output to the next one (which can use it as input).

* **Input-Assembler Stage** - The input assembler is responsible for supplying data to the pipeline. This stage takes its input from user-filled buffers in memory that contain arrays of vertices (and often also indices), and from those buffers it assembles primitives (triangles, lines and points) with attached system-generated values (such as a primitive ID, an instance ID, or a vertex ID) to pass to the next stage, vertex by vertex.
  
* **Vertex Shader Stage** - The vertex shader processes vertices from the input assembler. It usually performs operations such as transformations, skinning, and per-vertex lighting. A vertex shader always takes one vertex as input and return one vertex as output. So, a vertex shader needs to run multiple times to process all the vertices of the primitives assembled by the input assembler.
  
* **Hull Shader Stage** - The hull shader operates once per patch. A patch is a set of control points whose interpolation defines a piece of curve or surface. You can use this stage with patches from the input assembler. The hull shader can transform input control points into output control points. Also, it can perform other setup for the fixed-function tessellator stage. For example, the hull shader can output tess factors, which are numbers that indicate how much to tessellate, which refers to the primitive density in the surface or curve described by the output patch.

* **Tessellator Stage** - The tessellator is a fixed-function unit whose operation is defined by declarations in the hull shader. That is, the tessellator operates once per patch that is output by the hull shader. The purpose of the tessellator stage is to sample the domain (quad, tri, or line) of the patch by tessellating it. Also, the tess factors generated by the hull shader notify the tessellator how much to tessellate (that is, dividing a geometric shape into smaller, connected primitives) over the domain of the patch.

* **Domain Shader Stage** - The domain shader is invoked once per sample (generated by the tessellator). Each invocation is identified by the coordinates of the sample over the domain of the patch, as well as the output control points produced by the hull shader. The role of the domain shader is to turn the sample coordinates into something tangible (such as, a vertex position in 3D space), which can be sent down the pipeline for further processing.

* **Geometry Shader Stage** - The geometry shader processes entire primitives. That is, its input can be three vertices for a triangle, two vertices for a line, or a single vertex for a point. The geometry shader supports limited geometry amplification and de-amplification. Given an input primitive, the geometry shader can discard the primitive, or emit one or more new primitives.

* **Stream Output Stage** - The stream output is designed for streaming primitive data from the pipeline to memory on its way to the rasterizer. Data can be streamed out and/or passed into the rasterizer. Data streamed out to memory can be recirculated back into the pipeline as input data for the input assembler or read-back from the CPU.

* **Rasterizer Stage** - The rasterizer converts primitives into a raster images (composed of pixels that need to be processed by subsequent stages to be rendered on the screen). It also takes into account the viewport and scissor rectangles (more on this later). As part of the rasterization process, the per-vertex attributes are interpolated to calculate the per-pixel attributes across each primitive (a vertex is essentially a collection of attributes). Additionally, the rasterizer takes into account the viewport and scissor rectangles when performing its task. Further details will be provided in later sections of this tutorial.

* **Pixel Shader Stage** - The pixel shader receives interpolated attributes for every pixel of a rasterized primitive generated by the rasterizer and returns per-pixel data (such as color), such as a color, that can be possibly stored as a texel in the render target; it depends on the last stage of the pipeline. The pixel shader allows to enable sophisticated shading techniques, including per-pixel lighting and post-processing, to enhance the overall visual quality of the rendered image.

* **Output-Merger Stage** - The output merger (OM) stage is the final step for determining which pixels are visible, through depth-stencil testing, and how to eventually blend them with the corresponding texels in the render target. In particular, it is responsible for combining various types of output data (pixel shader returned values, depth, stencil and blend information) with the contents of the render target and depth/stencil buffer to generate the final pipeline result (the final image we want to draw on the render target before showing it on the screen).

<br>

```{note}
Hull Shader, Tessellator and Domain Shader together form the Tessellation stage. They are optional and we won't use them for a while. The same goes for the Geometry Shader and the Stream Output stage.
```

As illustrated in the figure above, many stages can take part of their input by reading resources from GPU memory (in addition to the input passed from the previous stage), and some of them can write their output in GPU memory as well. The little squares at the bottom right of some stages represent the slots where descriptors\views are bound. At the left of each stage, you can find a visual description of the task usually performed by the stage. `VSMAIN`, `PSMAIN`, etc., suggest that programmable stages execute shader programs.

```{note}
A descriptor (or view) is a data structure that fully describes a resource to the GPU (type, dimension, GPU virtual address, and other hardware-specific information). This means the size of a descriptor can change from GPU to GPU. Usually, descriptors are bound to the slots of the stages, rather than the actual resources. This way, the GPU can access resources by reading the descriptors bound to the slots used in the shader program being executed. 
```

```{important}
Binding slots are not physical blocks of memory or registers that a GPU can access to read descriptors. They are simple names (character strings) used to associate descriptors to resource declarations in shader programs. However, since the documentation uses the term "slots" in the context of resource binding, I still opted to represent them in the image above as physical blocks. As described in the previous tutorial, descriptors are stored in a descriptor heap. In the remainder of this tutorial we will see how a GPU accesses descriptors during the execution of a shader program.
```

```{note}
Even though the documentation does not explicitly distinguish between descriptors and views, it is sometimes helpful to visualize a descriptor as the physical block of memory where to store a view, which can be perceived as an instance of a hardware-specific type (structure) that encapsulates information about a resource.
```

The depth buffer is a texture associated with the render target (both should be the same size). The depth buffer is used to store depth information that informs us how deep each visible pixel is in the scene. When Direct3D renders 3D primitives to a render target, the output merger stage can use the depth buffer to determine how the pixels of rasterized polygons occlude one another.

Typically, the stencil buffer is coupled with the depth buffer (that is, both share the same buffer in memory, though using different bits) and stores stencil information to mask pixels. The mask controls whether a pixel is drawn or not on the render target, enabling the creation of specialized effects such as dissolves, decaling, and outlining. We will return to the stencil buffer in a later tutorial.

```{figure} images/02/depth-buffer.png
```

In the illustration above, the 2D projection window lies in the plane identified by the X- and Y-axes. We already know that the projection window is eventually mapped to the render target, so let's temporarily assume that we are directly drawing on the render target (without passing through the projection window). The Z-axis is used to measure the depth of the pixels (that is, their distance from the XY-plane). As you can see, the texel of the render target will store the pixel color of the yellow square, which occludes the pixel of the red triangle. Indeed, the value stored in the corresponding texel of the depth buffer (that is, at the same position of the texel in the render target) is the depth of the nearest pixel: the one of the yellow square, in this example. So, the pixel of the triangle will be discarded if the depth test is enabled. On the other hand, if the depth test is disabled, the texel of the render target will store the color of the last pixel processed by the pixel shader since it will always overwrite whatever color is stored in that texel. Observe that if blending is enabled, the pixel color is blended with the color stored in the corresponding texel of the render rather than overwriting it.

The following image shows how a generic programmable stage works.

```{figure} images/02/programmable-stage.png
```

* **Input Data**: A vertex shader receives its input from the input assembler stage; geometry and pixel shaders receive their inputs from the previous stage. This data typically consists of multiple values, called attributes, that describe various aspects of the geometric data being processed. For example, attributes might include the position or color associated with a vertex, or the texture coordinates for a pixel. Additional inputs include system-value semantics, which are consumable by the first stage in the pipeline to which they are applicable.

* **Output Data**: A shader generate output results to be passed on to the subsequent stage of the pipeline. For a geometry shader, the amount of data output from a single invocation can vary. Semantics also apply to elements of the output data to convey information about the intended use to the next stage. Semantics are required for all elements passed between shader stages.

* **Shader Code**: GPUs can read from memory, perform vector floating point and integer arithmetic operations, or flow control operations. We write shader programs in HLSL (High Level Shader Language), a procedural language similar to C, to instruct the GPU on the operations to execute.

* **Samplers**: Samplers define how to sample and filter textures.

* **Textures**: Textures can be filtered using samplers, or read on a per-texel basis directly with the **Load** intrinsic function.

* **Buffers**: A buffer is a collection of fully typed data grouped into elements (just like an array). Buffers are never filtered, but can be read from memory on a per-element basis.

* **Constant Buffers**: Constant buffers are optimized for shader constant-variables. They are designed for more frequent update from the CPU; therefore, they have additional size, layout, and access restrictions.

<br>

```{note}
I know! I should elaborate further on semantics. In general, data passed between pipeline stages is completely generic. A semantic is a textual name we can associate to every element in the input data to establish its intended use. You can associate arbitrary strings with no special meaning to elements of the input data as semantics. However, there are several predefined semantics with specific meanings when attached to elements of the input data. For example, **POSITION** and **COLOR**, which are pretty self explanatory. A system-value semantic is simply a semantic that start with "SV_", and that can be associated with additional data generated and\or consumed by the stages of the pipeline to pass and\or identify info in the input data with a special meaning. For example, pixel shaders can only write to elements associated with the **SV_depth** and **SV_Target** system-value semantics. Don't worry if it's not entirely clear at the moment. It's simpler than it may seem, and practical examples will be provided later in the tutorial, specifically in the final part when we examine the code of the sample.
```

This section provided only a brief overview of the rendering pipeline, so if this is your first encounter with these concepts, it's perfectly normal to experience some initial confusion. However, dont worry! We will revisit each of these topics in the following sections and upcoming tutorials to provide detailed explanations.

<br>

[WIP]


## References
```{bibliography}
:filter: docname in docnames
```