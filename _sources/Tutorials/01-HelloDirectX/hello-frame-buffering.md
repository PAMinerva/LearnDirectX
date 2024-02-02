# Hello Frame Buffering

<br>

```{figure} images/06/HelloFrameBuffering.png
```

## Introduction

In previous tutorials, we used CPU and GPU in a sequential, suboptimal way. Indeed, we used a single command allocator to record drawing commands for two buffers in the swap chain. This means that, to avoid overwriting commands still in use by the GPU, we had to flush the command queue before recording new commands for creating the next frame – since all commands were recorded in the same memory location. This resulted in a sequential workflow: the CPU recorded the commands to create a frame and waited for the GPU to complete the actual rendering work. In other words, we were not able to create frames in advance on the CPU timeline with respect to the GPU. This tutorial addresses the problem by introducing a separate command allocator for each buffer in the swap chain.

The sample we will review in this tutorial ([D3D12HelloFrameBuffering](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld)) is not much different from the samples we examined so far, as it simply shows a triangle on the screen (I changed the background color of the screenshot above to visually distinguish it from the screenshots of previous tutorials). However, for the first time, we will take advantage of the parallelism between CPU and GPU by creating frames in advance on the CPU timeline.

<br>

## CPU-GPU parallelism

In order to explain how to unleash parallelism between CPU and GPU, it can be useful to revisit the explanation provided in [](hello-window.md) about frame presentation, which is reiterated below for convenience.


````{admonition} from Hello Window
:class: seealso

**IDXGISwapChain::Present** allows presenting (to the user, on the screen) the frame just created on the CPU timeline (using the current back buffer as the render target). How does it work? Present operations occur on the graphics queue associated with the swap chain. That is, when you call **Present**, a present operation is recorded in the command queue associated with swap chain during its creation, and a request to present the frame is inserted in a queue called the present queue, waiting for the GPU to execute the commands to draw on the related back buffer. Since this happens only after recording all the commands needed to create the frame, you are sure the GPU reached the present operation in the command queue only at the very end (i.e., after executing all the other previous drawing commands). At that point, the frame associated with the request in the present queue is done, ready to be shown on the screen at the next vertical interval when the swap/flip between the back and present buffers takes place.

```{figure} images/01/rect853d.png
```

```{important}
When you create a frame and present it on the CPU timeline, nothing happens on the related back buffer until the GPU starts executing drawing commands in the related command list.
```

```{note}
**Present** also updates the index of the current back buffer in the swap chain so that the next frame will be created on the other buffer when it becomes available again as a render target.
```

Observe that **Present** takes, as its first parameter (called *SyncInterval*), a value that specifies how to synchronize the presentation of a frame with the vertical blank. For values greater than zero, it indicates the number of vertical intervals the frame waits in the present queue before getting ready to be presented on the screen, enabling v-sync. In this sample, we always pass 1 as an argument to this parameter to specify that we want to wait a single vertical interval. In a later tutorial, we will explore what it means if you pass 0 to this parameter.
````

While this explanation provides a basic understanding on frame presentation, it offers an oversimplified view of how frames are actually presented on the screen. Indeed, when a frame is presented, various factors can affect the presentation process, which depends on the presentation model (specified in the swap chain), window mode (full screen or windowed), and V-Sync settings (on or off). However, exploring every possible combination is beyond the scope of this tutorial. Therefore, I will focus on explaining what happens in the **D3D12HelloFrameBuffering** sample (and previous ones as well), where we create a swap chain that uses the flip model to present frames on the screen, with a window that cannot be switched to full screen, and V-Sync enabled (that is, when we pass 1 to *SyncInterval*). Other combinations will be covered in later tutorials.

[WIP]