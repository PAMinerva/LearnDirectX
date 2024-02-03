# Hello Frame Buffering

<br>

```{figure} images/06/HelloFrameBuffering.png
```

## Introduction

In previous tutorials, we used CPU and GPU in a sequential, suboptimal way. Indeed, we used a single command allocator to record drawing commands for two buffers in the swap chain. This means that, to avoid overwriting commands still in use by the GPU, we had to flush the command queue before recording new commands for creating the next frame – since all commands were recorded in the same memory location. This resulted in a sequential workflow: the CPU recorded the commands to create a frame and waited for the GPU to complete the actual rendering work. In other words, we were not able to create frames in advance on the CPU timeline with respect to the GPU. This tutorial addresses the problem by introducing a separate command allocator for each buffer in the swap chain.

The sample we will review in this tutorial ([D3D12HelloFrameBuffering](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld)) is not much different from the samples we examined so far, as it simply shows a triangle on the screen (I changed the background color of the screenshot above to visually distinguish it from the screenshots of previous tutorials). However, for the first time, we will take advantage of the parallelism between CPU and GPU by creating frames in advance on the CPU timeline.

<br>

## CPU-GPU parallelism

Before discussing how to unleash parallelism between CPU and GPU, it can be useful to revisit the explanation provided in a previous tutorial regarding frame presentation, which is replicated below for convenience.


````{admonition} from [](hello-window.md)
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

By specifying the flag **DXGI_SWAP_EFFECT_FLIP_DISCARD** during swap chain creation, we indicate our intention to use the flip model to present frames on the screen. When this model is employed with windowed applications (i.e., not full screen), a system service called the **Desktop Window Manager** (DWM) wakes up at every vertical blank and retrieves the latest completed back buffer in the swap chain of all graphics applications running on the desktop (including the offscreen buffers of all "normal" windowed applications). It then composes the final image of the entire desktop into its own back buffer, which will be displayed on the screen at the next vertical interval, when it becomes the present buffer.

```{figure} images/06/dwm.png
```

```{figure} images/06/dwm-composition.png
```

Let’s use a practical example to explain in more detail what happens under the conditions outlined above, where the flip model is used with a swap chain that includes two buffers for rendering in an application running in windowed mode. This will also allow us to illustrate how to unlock parallelism between CPU and GPU. For convenience, we'll refer to the two buffers in the swap chain as A and B.

As soon as we start the application, the commands to draw the very first frame on buffer A are recorded in a command list. Then, **ExecuteCommandLists** and **Present** are invoked to send the command list to the command queue and insert the first frame into the present queue, respectively. As a consequence, a present operation is also added to the command queue after the command list.

```{note}
Until now, we have only created the frame on the CPU timeline. By recording drawing commands in a command list and calling **Present**, we have simply submitted instructions to the GPU. The actual rendering process on the associated back buffer will not begin until the GPU starts executing those commands from the command queue.
```

Provided that we don't overwrite the memory space managed by the command allocator for the command list operating on buffer A (e.g., using a new allocator for the command list operating on buffer B), we can start creating (on the CPU timeline) a second frame with buffer B as the render target. We can queue this new frame with the confidence that the GPU won't write onto buffer B before it finishes drawing on buffer A (since command lists submitted through separate calls to **ExecuteCommandLists** are executed sequentially within the command queue; more on this shortly), or if buffer B is unavailable.

```{note}
A buffer is said to be available if there are no outstanding present operations that reference it, and it is currently not being displayed by the system. Otherwise, it is unavailable.<br>
A frame is considered ready to be shown on the screen when the GPU met the related present operation in the command queue, and if the synchronization time associated with the presentation of the frame in the present queue expired.<br>
A frame is retired from the present queue if discarded by the DWM (more on this shortly), or when replaced by a new frame as the one displayed on the screen.
```

[WIP]