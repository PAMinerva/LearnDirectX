# Hello Window

![Hello Window](images/A/HelloWindow.png)

## Introduction
DirectX provides a set of APIs that can be used to create games and graphics applications. Specifically, it includes support for high-performance 2-D and 3-D graphics, audio, arithmetic, and linear algebra operations. Below is a list of the main APIs included in DirectX. However, our primary focus is on Direct3D 12 and DirectXMath, the only ones we will be using for a while.

<br>

**Direct3D** provides functionality for performing 3-D graphics rendering tasks. It is used to draw primitives (i.e., points, lines, and triangles) within the rendering pipeline or to start parallel operations on the GPU. When we refer to Direct3D 12, we are specifically talking about the API that enables apps to leverage the graphics and computing capabilities of PCs equipped with a DirectX 12-compatible GPU.

**Direct2D** offers functionality for rendering 2-D geometries, bitmaps, and text. It is designed to interoperate with existing code that uses Direct3D to create 2D menus, user interface (UI) elements, and Heads-up Displays (HUDs).

**DirectWrite** provides support for high-quality text rendering, resolution-independent outline fonts, and full Unicode text and layouts. It is designed to interoperate with Direct2D to render text by taking advantage of hardware acceleration. Text can also be filled with an arbitrary Direct2D brush, such as radial gradients, linear gradients, and bitmaps.

**DirectXMath** provides types and helper functions for common linear algebra and graphics math operations that are frequently used in DirectX applications.

**XAudio2** allows the addition of sound effects and background music, or the development of high-performance audio engines.

**XInput** enables applications to receive input from the Xbox Controller when it is connected to a Windows PC.

<br>

To create graphics applications, you first need a window to draw on. Therefore, the aim of this tutorial is to create and display a simple window on your screen. For this purpose, we will examine the [D3D12HelloWindow](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld) sample, which is part of the [DirectX-Graphics-Samples](https://github.com/microsoft/DirectX-Graphics-Samples) repository maintained by Microsoft. The only significant graphics operation performed by this sample is the setting of the window background color. You might be surprised to discover that you need to write a substantial amount of code to execute this simple operation. The good news is that the code we will review in this first tutorial primarily consists of boilerplate code. This means that, by the end of this tutorial, you will have a basic understanding of the common framework used by almost all samples we will examine in the upcoming tutorials, so we can solely focus on new additions.

Before starting to review the source code of the [D3D12HelloWindow](https://github.com/microsoft/DirectX-Graphics-Samples/tree/master/Samples/Desktop/D3D12HelloWorld) sample, you need a basic understanding of the Component Object Model (COM), the DXGI API, as well as how Windows applications work. You can skip the following three sections if you are comfortable with these topics. Alternatively, you can also refer to {cite}`AboutWindows,ProgrammingDirectXWithCOM,DXGIOverview` for further information.

<br>

## Windows applications
This section is heavily inspired by the first chapter of the book “Programming Microsoft Visual C++, Fifth Edition” by David J. Kruglinski, George Shepherd and Scott Wingo.

Windows applications use an event-driven programming model, as illustrated in the following below. In this model, programs respond to events by processing messages sent by the operating system. An event could be a keystroke, a mouse click, or a command for a window to repaint itself. The entry point of a Windows application is a function called **WinMain**, but most of the action occurs in a function known as the window procedure. The window procedure processes messages sent by the OS to the application that a window belongs to. **WinMain** creates that window and then enters a message loop, retrieving messages and dispatching them to the window procedure. Messages wait in a message queue until they are retrieved. The primary task of a Windows application is to respond to the messages it receives. In between messages, it does little except wait for the next message to arrive. An application can exit the message loop when a **WM_QUIT** message is retrieved from the message queue, signaling that the application is about to end. This message is sent by the OS when the user closes the window. When the message loop ends, **WinMain** returns, and the application terminates.

<br>

![Image](images/A/rect31572.png)

<br>

Note that window messages can also be sent directly to a window procedure, bypassing the message queue. If the sending thread is dispatching a message to a window created by the same thread, the window procedure of the specified window is invoked. However, if a thread is dispatching a message to a window created by a different thread, the process becomes more complex. Fortunately, we don’t need to delve into the low-level details in this tutorial series.


### Window Procedure

As previously mentioned, a window procedure is a function that receives and processes messages sent by the operating system to the application that a window belongs to. A window class defines key characteristics of a window, such as its window procedure address, its default background color, and its icon. Every window created with a specific class will use the same window procedure to respond to messages.

When the application dispatches a message to a window procedure, it also passes additional information about the message as arguments in its input parameters. This allows the window procedure to perform an appropriate action for a message by consuming the related message data. If a window procedure does not process a message, it must return the message to the system for default processing by calling the **DefWindowProc** function, which performs a default action and returns a message result. The window procedure must then return this value as its own message result.

Since a window procedure is shared by all windows belonging to the same class, it can process messages for different windows. To identify the specific window a message is addressed to, a window procedure can examine the window handle passed as an input parameter. The code provided in the window procedure to process a particular message is known as a message handler.


### Messages

Windows defines many different message types. Usually, messages have names that begin with the letters "WM_", as in **WM_CREATE** and **WM_PAINT**. The following table shows ten of the most common messages.

<br>

| Message        | Sent when                                                                      |
| -------------- | ------------------------------------------------------------------------------ |
| WM_CHAR        | A character is input from the keyboard.                                        |
| WM_COMMAND     | The user selects a menu item, or a control sends a notification to its parent. |
| WM_CREATE      | A window is created.                                                           |
| WM_DESTROY     | A window is destroyed.                                                         |
| WM_LBUTTONDOWN | The left mouse button is pressed.                                              |
| WM_LBUTTONUP   | The left mouse button is released.                                             |
| WM_MOUSEMOVE   | The mouse pointer is moved.                                                    |
| WM_PAINT       | A window needs repainting.                                                     |
| WM_QUIT        | The application is about to terminate.                                         |
| WM_SIZE        | A window is resized.                                                           |

<br>

For example, a window receives a **WM_PAINT** message when its interior needs repainting. You can think of a Windows program as a collection of message handlers.

When the message loop dispatches a message, the window procedure is called, and you can retrieve the information on the message from its four input parameters:

* The handle of the window to which the message is directed,

* A message ID, and

* Two 32-bit parameters known as **wParam** and **lParam**.

<br>

The window handle is a 32-bit value that uniquely identifies a window. Internally, the value references a data structure in which the OS stores relevant information about the window such as its size, style, and location on the screen.

The message ID is a numeric value that identifies the message type: **WM_CREATE**, **WM_PAINT**, and so on.

**wParam** and **lParam** contain information specific to the message type. For example, when a **WM_LBUTTONDOWN** message arrives, **wParam** holds a series of bit flags identifying the state of the <kbd>Ctrl</kbd> and <kbd>Shift</kbd> keys and of the mouse buttons. **lParam** holds two 16-bit values identifying the location of the mouse pointer (in screen coordinates) when the click occurred. At that point, you have all you need to know to process the **WM_LBUTTONDOWN** message in the window procedure. Conventionally, **WinMain** should return the value stored in the **wParam** of the **WM_QUIT** message.

The only criticism to the above explanation is that, unlike typical window applications, graphics applications perform the majority of their processing in between messages. However, **D3D12HelloWindow** is an exception as its sole purpose is to display a window on the screen (i.e., no significant graphics operations are involved).

<br>

[WIP]

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