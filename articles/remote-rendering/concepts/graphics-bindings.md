---
title: 图形绑定
description: 设置图形绑定和用例
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681878"
---
# <a name="graphics-binding"></a>图形绑定

为了能够在自定义应用程序中使用 Azure 远程呈现，需要将其集成到应用程序的呈现管道中。 此集成是图形绑定的责任。

设置后，图形绑定允许访问影响渲染图像的各种功能。 这些函数可以分为两类：始终可用的常规函数和仅与所选`Microsoft.Azure.RemoteRendering.GraphicsApiType`函数相关的特定函数。

## <a name="graphics-binding-in-unity"></a>统一中的图形绑定

在 Unity 中，整个绑定由传递到`RemoteUnityClientInit`的结构处理`RemoteManagerUnity.InitializeManager`。 要设置图形模式，必须将`GraphicsApiType`字段设置为所选绑定。 根据是否存在 XRDevice，将自动填充该字段。 可以通过以下行为手动重写该行为：

* **HoloLens 2**： 始终使用[Windows 混合现实](#windows-mixed-reality)图形绑定。
* **平面 UWP 桌面应用程序**：始终使用[模拟](#simulation)。 要使用此模式，请确保按照教程中的步骤[：从头开始设置 Unity 项目](../tutorials/unity/project-setup.md)。
* **Unity 编辑器**：除非连接 WMR VR 耳机，否则始终使用[仿真](#simulation)，在这种情况下，ARR 将被禁用，以允许调试应用程序的非 ARR 相关部分。 另请参阅[全息远程处理](../how-tos/unity/holographic-remoting.md)。

Unity 的唯一其他相关部分是访问[基本绑定](#access)，可以跳过下面的所有其他部分。

## <a name="graphics-binding-setup-in-custom-applications"></a>自定义应用程序中的图形绑定设置

要选择图形绑定，请执行以下两个步骤：首先，在初始化程序时，必须静态初始化图形绑定：

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

上述调用对于将 Azure 远程呈现初始化到全息 API 是必需的。 在调用任何全息 API 之前和在访问任何其他远程呈现 API 之前，必须调用此功能。 同样，在不再调用全息 API`RemoteManagerStatic.ShutdownRemoteRendering();`后，应调用相应的去init函数。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">访问图形绑定

设置客户端后，可以使用`AzureSession.GraphicsBinding`getter 访问基本图形绑定。 例如，可以像这样检索最后一个帧统计信息：

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>图形 API

当前可以选择两个图形 API，`WmrD3D11`和`SimD3D11`。 第三个`Headless`存在，但客户端上尚未支持。

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`是在 HoloLens 2 上运行的默认绑定。 它将创建`GraphicsBindingWmrD3d11`绑定。 在此模式下，Azure 远程呈现直接挂钩到全息 API 中。

要访问派生的图形绑定，必须强制转换`GraphicsBinding`基。
使用 WMR 绑定需要执行两项操作：

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>通知已使用坐标系的远程渲染

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

上面`ptr`必须是指向定义世界空间坐标系的本机`ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem`对象的指针，其中表示 API 中的坐标。

#### <a name="render-remote-image"></a>渲染远程图像

在每个帧的开头，需要将远程帧呈现到后台缓冲区中。 这是通过调用`BlitRemoteFrame`来完成的，这将填充当前绑定渲染目标中的颜色和深度信息。 因此，在将后台缓冲区绑定为渲染目标后，请务必这样做。

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>模拟

`GraphicsApiType.SimD3D11`是模拟绑定，如果选中，它将创建`GraphicsBindingSimD3d11`图形绑定。 此接口用于模拟头部移动，例如在桌面应用程序中并渲染单镜图像。
设置涉及更多，工作如下：

#### <a name="create-proxy-render-target"></a>创建代理呈现目标

使用`GraphicsBindingSimD3d11.Update`函数提供的代理摄像机数据，需要将远程和本地内容渲染到称为"代理"的屏幕外颜色/深度渲染目标。 代理必须匹配后台缓冲区的分辨率。 会话准备就绪后，`GraphicsBindingSimD3d11.InitSimulation`需要在连接到会话之前调用它：

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Init 函数需要提供指向本机 d3d 设备的指针以及代理渲染目标的颜色和深度纹理。 初始化后，`AzureSession.ConnectToRuntime``DisconnectFromRuntime`可以多次调用，但在切换到其他会话时，`GraphicsBindingSimD3d11.DeinitSimulation`需要在旧会话上首先调用，然后才能`GraphicsBindingSimD3d11.InitSimulation`在另一个会话上调用。

#### <a name="render-loop-update"></a>渲染循环更新

渲染循环更新由多个步骤组成：

1. 在进行任何渲染之前，`GraphicsBindingSimD3d11.Update`使用发送到要渲染的服务器的当前摄像机转换调用每个帧。 同时，返回的代理转换应用于代理摄像机以呈现为代理渲染目标。
如果返回的代理更新`SimulationUpdate.frameId`为空，则尚未有远程数据。 在这种情况下，不应渲染到代理渲染目标，任何本地内容都应直接使用当前摄像机数据呈现到后台缓冲区，并跳过接下来的两个步骤。
1. 应用程序现在应绑定代理呈现目标和调用`GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 这将将远程颜色和深度信息填充到代理渲染目标中。 现在可以使用代理摄像机转换将任何本地内容呈现到代理上。
1. 接下来，后台缓冲区需要绑定为渲染目标，并`GraphicsBindingSimD3d11.ReprojectProxy`调用可以在此时显示后台缓冲区。

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>后续步骤

* [教程：从头开始设置 Unity 项目](../tutorials/unity/project-setup.md)
