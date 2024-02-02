# Hello Frame Buffering

<br>

```{figure} images/06/HelloFrameBuffering.png
```

## Introduction

In previous tutorials, we used CPU and GPU in a sequential, suboptimal way. Indeed, we used a single command allocator to record drawing commands for two buffers in the swap chain. This means that, to avoid overwriting commands still in use by the GPU, we had to flush the command queue before recording new commands for creating the next frame – since all commands were recorded in the same memory location. This resulted in a sequential workflow: the CPU recorded the commands to create a frame and waited for the GPU to complete the actual rendering work. In other words, we were not able to create frames in advance on the CPU timeline with respect to the GPU. This tutorial addresses the problem by introducing a separate command allocator for each buffer in the swap chain.

The sample we will review in this tutorial ([D3D12HelloFrameBuffering](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld)) is not much different from the samples we examined so far, as it simply shows a triangle on the screen (I changed the background color of the screenshot above to visually distinguish it from the screenshots of previous tutorials). However, for the first time, we will take advantage of the parallelism between CPU and GPU by creating frames in advance on the CPU timeline.

[WIP]