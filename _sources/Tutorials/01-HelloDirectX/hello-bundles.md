# Hello Bundles

<br>

```{figure} images/03/HelloBundles.png
```

## Introduction

The sample we will review in this tutorial ([D3D12HelloBundles](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld)) is not much different from the sample examined in [](hello-triangle.md), as they both use the same vertex buffer, shaders and commands to render a triangle on the screen (I only changed the background color of the window's client area to visually distinguish between them). The only main difference is the way we record some commands in the command list. In the implementation of the **D3D12HelloBundles** sample, we will make use of a bundle. Bundles allow the GPU to speed up the execution of groups of commands in some specific cases. The online documentation for bundles is clear, so the next theoretical sections of this tutorial will simply summarize information already accessible on the Microsoft website (see {cite}`Direct3D12Graphics`).

<br>

## Bundles

Up until now, we've used command lists to record commands for the GPU to execute. These command lists are commonly referred to as direct command lists. However, the Direct3D 12 API also introduces a secondary level of command lists called bundles, which can be executed within direct command lists. Bundles take advantage of functionality present in all graphics hardware to allow applications to group a limited number of commands together for later, efficient and repeated execution from within direct command lists. At the time of creating a bundle, the driver performs extensive pre-processing to optimize its later execution on the GPU. Bundles can then be executed from within multiple direct command lists or multiple times within the same direct command list. However, since bundles are pre-processed and can be submitted multiple times, there are certain restrictions on the operations that can be performed within a bundle, which are detailed in Section 4.

A direct command list can be submitted multiple times for execution, but it's crucial for the application or programmer to guarantee that the command list has fully executed on the GPU timeline prior to recording and resubmitting it. This is because when new commands are recorded into a command list to create a new frame, the memory region that holds the previous commands gets overwritten. In contrast, bundles don't have concurrency limitations and can be executed multiple times, even across different direct command lists, as they retain the original commands for repeated usage. However, it's essential to remember that bundles can only be invoked from direct command lists and cannot be submitted directly to a queue.

Direct command lists and bundles are both created using the **ID3D12Device::CreateCommandList** method and use a command allocator to handle the memory region containing the commands. The key distinction lies in the **D3D12_COMMAND_LIST_TYPE** enumeration passed as an argument, which specifies whether the command list being created is a direct command list or a bundle. <br>
A direct command list can record the execution of a group of commands within a bundle by invoking the **ID3D12GraphicsCommandList::ExecuteBundle** method.

The image below illustrates an example of how to organize some commands into a couple of bundles and how to record these bundles across different direct command lists to render a frame. In this case, three commands are bundled into bundle 1, which is then recorded in direct command list 2. On the other hand, bundle 2 isreused in direct command lists 2 and 3.

```{figure} images/03/bundles.png
```

```{tip}
We can use multiple command lists submitted from separated threads to create a single frame, perhaps to render different parts of it or to pre-compute rendering work for later re-use. This allows the spreading of "rendering" (actually recording) work across multiple threads. We will cover multithreading in a later tutorial.
```

<br>

## Graphics pipeline state inheritance

Because direct command lists are generally intended for one use at a time, and bundles are intended to be used multiple times concurrently, there are different rules about how they inherit graphics pipeline state that was set by previous command lists or bundles.

For the graphics pipeline states that are set using PSOs, none of these states are inherited by either direct command lists or bundles. The initial graphics pipeline state for both direct command lists and bundles is set at creation time by passing a PSO to **ID3D12Device::CreateCommandList**. If no PSO is specified in the call, a default initial state is used. You can change the current PSO within a command list by calling **ID3D12GraphicsCommandList::SetPipelineState**.

Direct command lists also do not inherit non-PSO states that is set with command list methods like **RSSetViewports**, **RSSetScissorRects**, **OMSetStencilRef**, **OMSetBlendFactor**, etc.

Bundles inherit all graphics pipeline states that are not set with PSOs, except for the primitive topology type. The primitive topology is always set to **D3D12_PRIMITIVE_TOPOLOGY_TYPE_UNDEFINED** when a bundle begins executing. <br>
Any state that is set within a bundle (the PSO itself, non-PSO-based state, and resource bindings) affects the state of its parent direct command list. For example, if a **RSSetViewports** is called from within a bundle, the specified viewports will continue to be set in the parent direct command list for calls subsequent to the **ExecuteBundle** call that set the viewports.

Resource bindings that are set within a command list or bundle do persist. So, resource bindings modified in a direct command list will still be set within subsequent child bundle execution. And resource bindings modified from within a bundle will still be set for subsequent calls within the parent direct command list.

<br>

## Bundle restrictions

Restrictions on what operations can be performed within a bundle enable graphics drivers to do most of the work associated with bundles at record time, thus enabling **ExecuteBundle** to be run with low overhead. All pipeline state objects referenced by a bundle must have the same render target formats, depth buffer format, and sample descriptions. The following command list API calls (commands) are not allowed on command lists created with type: **D3D12_COMMAND_LIST_TYPE_BUNDLE**:

- Any Clear method
- Any Copy method
- **DiscardResource**
- **ExecuteBundle**
- **ResourceBarrier**
- **ResolveSubresource**
- **SetPredication**
- **BeginQuery**
- **EndQuery**
- **SOSetTargets**
- **OMSetRenderTargets**
- **RSSetViewports**
- **RSSetScissorRects**

```{note}
**SetDescriptorHeaps** can be called on a bundle, but the bundle descriptor heaps must match the calling command list descriptor heap. <br>
If any of these APIs are called on a bundle, the runtime will drop the call. The debug layer will issue an error whenever this occurs.
```

<br>

## D3D12HelloBundles: code review

In previous tutorials, we used **PopulateCommandList** as the only function for recording commands in the command list. Let's revisit this function again by showing its implementation from the previous tutorial ([](hello-triangle.md)).

```{code-block} cpp
:caption: HelloTriangle/D3D12HelloTriangle.cpp

void D3D12HelloTriangle::PopulateCommandList()
{
    // Command list allocators can only be reset when the associated 
    // command lists have finished execution on the GPU; apps should use 
    // fences to determine GPU execution progress.
    ThrowIfFailed(m_commandAllocator->Reset());
 
    // However, when ExecuteCommandList() is called on a particular command 
    // list, that command list can then be reset at any time and must be before 
    // re-recording.
    ThrowIfFailed(m_commandList->Reset(m_commandAllocator.Get(), m_pipelineState.Get()));
 
    // Set necessary state.
    m_commandList->SetGraphicsRootSignature(m_rootSignature.Get());
    m_commandList->RSSetViewports(1, &m_viewport);
    m_commandList->RSSetScissorRects(1, &m_scissorRect);
 
    // Indicate that the back buffer will be used as a render target.
    m_commandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(m_renderTargets[m_frameIndex].Get(), D3D12_RESOURCE_STATE_PRESENT, D3D12_RESOURCE_STATE_RENDER_TARGET));
 
    CD3DX12_CPU_DESCRIPTOR_HANDLE rtvHandle(m_rtvHeap->GetCPUDescriptorHandleForHeapStart(), m_frameIndex, m_rtvDescriptorSize);
    m_commandList->OMSetRenderTargets(1, &rtvHandle, FALSE, nullptr);
 
    // Record commands.
    const float clearColor[] = { 0.0f, 0.2f, 0.4f, 1.0f };
    m_commandList->ClearRenderTargetView(rtvHandle, clearColor, 0, nullptr);
    m_commandList->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
    m_commandList->IASetVertexBuffers(0, 1, &m_vertexBufferView);
    m_commandList->DrawInstanced(3, 1, 0, 0);
 
    // Indicate that the back buffer will now be used to present.
    m_commandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(m_renderTargets[m_frameIndex].Get(), D3D12_RESOURCE_STATE_RENDER_TARGET, D3D12_RESOURCE_STATE_PRESENT));
 
    ThrowIfFailed(m_commandList->Close());
}
```

We call this function whenever the window needs repainting. As a result, the GPU always ends up executing the same commands. Let’s explore the possibility of building up a bundle by grouping some of these commands. **SetGraphicsRootSignature**, **IASetPrimitiveTopology**, **IASetVertexBuffers** and **DrawInstanced** can be used in a bundle, while **RSSetViewports**, **RSSetScissorRects**, **ResourceBarrier**, and **OMSetRenderTargets** cannot (as stated in the previous section).

At this point, we can finally take a look at the code of the **D3D12HelloBundles** sample.<br>
In the application class, we declare a bundle (as a **ID3D12GraphicsCommandList**), along with the related allocator.

```{code-block} cpp
:caption: HelloBundles/D3D12HelloBundles.cpp
:name: hellobundles-code

class D3D12HelloBundles : public DXSample
{
public:
    D3D12HelloBundles(UINT width, UINT height, std::wstring name);
 
    virtual void OnInit();
    virtual void OnUpdate();
    virtual void OnRender();
    virtual void OnDestroy();
 
private:
    static const UINT FrameCount = 2;
 
    struct Vertex
    {
        XMFLOAT3 position;
        XMFLOAT4 color;
    };
 
    // Pipeline objects.
    CD3DX12_VIEWPORT m_viewport;
    CD3DX12_RECT m_scissorRect;
    ComPtr<IDXGISwapChain3> m_swapChain;
    ComPtr<ID3D12Device> m_device;
    ComPtr<ID3D12Resource> m_renderTargets[FrameCount];
    ComPtr<ID3D12CommandAllocator> m_commandAllocator;
    ComPtr<ID3D12CommandAllocator> m_bundleAllocator;
    ComPtr<ID3D12CommandQueue> m_commandQueue;
    ComPtr<ID3D12RootSignature> m_rootSignature;
    ComPtr<ID3D12DescriptorHeap> m_rtvHeap;
    ComPtr<ID3D12PipelineState> m_pipelineState;
    ComPtr<ID3D12GraphicsCommandList> m_commandList;
    ComPtr<ID3D12GraphicsCommandList> m_bundle;
    UINT m_rtvDescriptorSize;
 
    // App resources.
    ComPtr<ID3D12Resource> m_vertexBuffer;
    D3D12_VERTEX_BUFFER_VIEW m_vertexBufferView;
 
    // Synchronization objects.
    UINT m_frameIndex;
    HANDLE m_fenceEvent;
    ComPtr<ID3D12Fence> m_fence;
    UINT64 m_fenceValue;
 
    void LoadPipeline();
    void LoadAssets();
    void PopulateCommandList();
    void WaitForPreviousFrame();
};
```

**LoadPipeline** creates the allocators. We specify **BUNDLE** as the command list type for **m_bundleAllocator**. This way, we are indicating that the allocator will manage storage for a bundle rather than for a direct command list.

```{code-block} cpp
:caption: HelloBundles/D3D12HelloBundles.cpp
:name: hellobundles-LoadPipeline-code

// Load the rendering pipeline dependencies.
void D3D12HelloBundles::LoadPipeline()
{

 
    // ...

 
    ThrowIfFailed(m_device->CreateCommandAllocator(D3D12_COMMAND_LIST_TYPE_DIRECT, IID_PPV_ARGS(&m_commandAllocator)));
    ThrowIfFailed(m_device->CreateCommandAllocator(D3D12_COMMAND_LIST_TYPE_BUNDLE, IID_PPV_ARGS(&m_bundleAllocator)));
}
```

**LoadAssets** creates both the direct command list and the bundle. Moreover, it records the commands in the bundle to let the driver perform its work as soon as possible. Observe that we close the bundle, as **ExecuteBundle** requires it.

```{code-block} cpp
:caption: HelloBundles/D3D12HelloBundles.cpp
:name: hellobundles-LoadAssets-code

// Load the sample assets.
void D3D12HelloBundles::LoadAssets()
{

    // ...

 
    // Create the command list.
    ThrowIfFailed(m_device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_DIRECT, m_commandAllocator.Get(), m_pipelineState.Get(), IID_PPV_ARGS(&m_commandList)));
 
    // Command lists are created in the recording state, but there is nothing
    // to record yet. The main loop expects it to be closed, so close it now.
    ThrowIfFailed(m_commandList->Close());

 
    // ...
    
 
    // Create and record the bundle.
    {
        ThrowIfFailed(m_device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_BUNDLE, m_bundleAllocator.Get(), m_pipelineState.Get(), IID_PPV_ARGS(&m_bundle)));
        m_bundle->SetGraphicsRootSignature(m_rootSignature.Get());
        m_bundle->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
        m_bundle->IASetVertexBuffers(0, 1, &m_vertexBufferView);
        m_bundle->DrawInstanced(3, 1, 0, 0);
        ThrowIfFailed(m_bundle->Close());
    }
 
    // ...

}
```

And finally, we can examine the code of our revised version of the **PopulateCommandList** function.

```{code-block} cpp
:caption: HelloBundles/D3D12HelloBundles.cpp
:name: hellobundles-PopulateCommandList-code
:emphasize-lines: 14

void D3D12HelloBundles::PopulateCommandList()
{
    // Command list allocators can only be reset when the associated 
    // command lists have finished execution on the GPU; apps should use 
    // fences to determine GPU execution progress.
    ThrowIfFailed(m_commandAllocator->Reset());
 
    // However, when ExecuteCommandList() is called on a particular command 
    // list, that command list can then be reset at any time and must be before 
    // re-recording.
    ThrowIfFailed(m_commandList->Reset(m_commandAllocator.Get(), m_pipelineState.Get()));
 
    // Set necessary state.
    //m_commandList->SetGraphicsRootSignature(m_rootSignature.Get()); // REDUNDANT?!?
    m_commandList->RSSetViewports(1, &m_viewport);
    m_commandList->RSSetScissorRects(1, &m_scissorRect);
 
    // Indicate that the back buffer will be used as a render target.
    m_commandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(m_renderTargets[m_frameIndex].Get(), D3D12_RESOURCE_STATE_PRESENT, D3D12_RESOURCE_STATE_RENDER_TARGET));
 
    CD3DX12_CPU_DESCRIPTOR_HANDLE rtvHandle(m_rtvHeap->GetCPUDescriptorHandleForHeapStart(), m_frameIndex, m_rtvDescriptorSize);
    m_commandList->OMSetRenderTargets(1, &rtvHandle, FALSE, nullptr);
 
    // Record commands.
    const float clearColor[] = { 0.2f, 0.5f, 1.0f, 1.0f };
    m_commandList->ClearRenderTargetView(rtvHandle, clearColor, 0, nullptr);
 
    // Execute the commands stored in the bundle.
    m_commandList->ExecuteBundle(m_bundle.Get());
 
    // Indicate that the back buffer will now be used to present.
    m_commandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(m_renderTargets[m_frameIndex].Get(), D3D12_RESOURCE_STATE_RENDER_TARGET, D3D12_RESOURCE_STATE_PRESENT));
 
    ThrowIfFailed(m_commandList->Close());
}
```

We call **ExecuteBundle** on the direct command list to record the execution of the group of commands previously recorded in the bundle.

```{attention}
In the official sample provided by Microsoft, **SetGraphicsRootSignature** is recorded both in the bundle (see {numref}`hellobundles-LoadAssets-code`) and in the direct command list (see the highlighted instruction in {numref}`hellobundles-PopulateCommandList-code`). I commented the latter out since it should be redundant. Please let me know, by opening a new issue or discussion in the repository of this tutorial series, if you are aware of any reasons why this might not be a mistake.
```
<br>

## Source Code

[D3D12HelloWorld (DirectX-Graphics-Samples)](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld)

<br>

````{admonition} Support this project
If you found the content of this tutorial somewhat useful or interesting, please consider supporting this project by clicking on the Sponsor button below. Whether a small tip, a one-time donation, or a recurring payment, all contributions are welcome! Thank you!

```{figure} ../../sponsor.png
:align: center
:target: https://github.com/sponsors/PAMinerva

```
````

<br>

## References
```{bibliography}
:filter: docname in docnames
```