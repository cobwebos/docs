---
title: 图形绑定
description: 图形绑定和用例的设置
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681878"
---
# <a name="graphics-binding"></a>图形绑定

若要在自定义应用程序中使用 Azure 远程呈现，则需要将其集成到应用程序的呈现管道中。 此集成是图形绑定的责任。

设置后，图形绑定可以访问影响呈现图像的各种功能。 这些函数可以分为两个类别：始终可用的常规函数和仅与所选`Microsoft.Azure.RemoteRendering.GraphicsApiType`的相关的特定函数。

## <a name="graphics-binding-in-unity"></a>Unity 中的图形绑定

在 Unity 中，整个绑定由传入的`RemoteUnityClientInit`结构处理。 `RemoteManagerUnity.InitializeManager` 若要设置图形模式，必须`GraphicsApiType`将该字段设置为所选绑定。 将自动填充该字段，具体取决于是否存在 XRDevice。 可以通过以下行为手动重写此行为：

* **HoloLens 2**：始终使用[Windows Mixed Reality](#windows-mixed-reality)图形绑定。
* **平面 UWP 桌面应用**：始终使用[模拟](#simulation)。 若要使用此模式，请确保遵循[教程：从头开始设置 Unity 项目](../tutorials/unity/project-setup.md)中的步骤。
* **Unity 编辑器**：始终使用[模拟](#simulation)，除非连接了 WMR VR 耳机，在这种情况下，将禁用 ARR，以允许调试应用程序的非 ARR 相关部分。 另请参阅[全息远程处理](../how-tos/unity/holographic-remoting.md)。

Unity 的唯一其他相关部分是访问[基本绑定](#access)，可跳过下面的所有其他部分。

## <a name="graphics-binding-setup-in-custom-applications"></a>自定义应用程序中的图形绑定设置

若要选择图形绑定，请执行以下两个步骤：首先，在初始化程序时，必须对图形绑定进行静态初始化：

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

若要将 Azure 远程呈现初始化到全息 Api，需要调用上述的。 必须先调用此函数，然后才能调用任何全息 API，并在访问任何其他远程呈现 Api 之前调用此函数。 同样，在不再调用全息 Api 之后`RemoteManagerStatic.ShutdownRemoteRendering();` ，应调用相应的取消初始化函数。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">访问图形绑定

设置客户端后，可以使用`AzureSession.GraphicsBinding` getter 访问基本图形绑定。 例如，可以如下所示检索最后一帧统计信息：

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

## <a name="graphic-apis"></a>图形 Api

目前有两个图形 Api 可供选择： `WmrD3D11`和。 `SimD3D11` 第三个`Headless`存在，但在客户端尚不支持。

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`是要在 HoloLens 2 上运行的默认绑定。 它将创建`GraphicsBindingWmrD3d11`绑定。 在此模式下，Azure 远程呈现挂钩直接加入全息 Api。

若要访问派生的图形绑定，必须`GraphicsBinding`强制转换基准。
若要使用 WMR 绑定，需要执行以下两项操作：

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>通知已用坐标系统的远程呈现

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

其中，上面`ptr`必须是一个指向本机`ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem`对象的指针，该对象定义将在其中表示 API 中的坐标的世界空间坐标系统。

#### <a name="render-remote-image"></a>呈现远程映像

每个帧开始时，需要将远程帧呈现到后台缓冲区。 这是通过调用`BlitRemoteFrame`来完成的，这会将颜色和深度信息填充到当前绑定的呈现器目标。 因此，将后台缓冲区绑定为呈现器目标后，这一点非常重要。

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>模拟

`GraphicsApiType.SimD3D11`为模拟绑定，如果选中，则创建`GraphicsBindingSimD3d11`图形绑定。 此接口用于模拟头运动，例如，在桌面应用程序中，并呈现 monoscopic 图像。
此设置更多一些，并且工作方式如下所示：

#### <a name="create-proxy-render-target"></a>创建代理呈现目标

远程和本地内容需要使用该`GraphicsBindingSimD3d11.Update`函数提供的代理照相机数据呈现为一个屏幕外/深度呈现器目标（称为 "代理"）。 代理必须与后台缓冲区的分辨率匹配。 会话准备就绪后，需要`GraphicsBindingSimD3d11.InitSimulation`先调用，然后才能连接到该会话：

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

Init 函数需要随指向本机 d3d-设备的指针以及代理呈现目标的颜色和深度纹理一起提供。 初始化后， `AzureSession.ConnectToRuntime` `DisconnectFromRuntime`可以多次调用，但在切换到不同会话时， `GraphicsBindingSimD3d11.DeinitSimulation`需要先在旧会话上调用，然后`GraphicsBindingSimD3d11.InitSimulation`才能在另一个会话上调用。

#### <a name="render-loop-update"></a>呈现循环更新

呈现循环更新包含多个步骤：

1. 在发生任何呈现之前， `GraphicsBindingSimD3d11.Update`将使用发送到要呈现的服务器的当前相机转换调用每个帧。 同时，返回的代理转换应应用到代理照相机以呈现给代理呈现目标。
如果返回的代理更新`SimulationUpdate.frameId`为 null，则尚无远程数据。 在这种情况下，应使用当前相机数据将任何本地内容直接呈现给后台缓冲区，并跳过接下来的两个步骤。
1. 现在，应用程序应绑定代理呈现目标并调用`GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 这会将远程颜色和深度信息填充到代理呈现器目标。 现在可以使用代理相机转换将任何本地内容呈现到代理上。
1. 接下来，需要将后台缓冲区绑定为呈现器目标并调用`GraphicsBindingSimD3d11.ReprojectProxy` ，此时将显示后台缓冲区。

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
