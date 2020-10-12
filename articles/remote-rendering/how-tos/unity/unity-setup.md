---
title: 为 Unity 设置远程渲染
description: 如何在 Unity 项目中初始化 Azure 远程呈现
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018971"
---
# <a name="set-up-remote-rendering-for-unity"></a>为 Unity 设置远程渲染

若要在 Unity 中启用 (ARR) 的 Azure 远程呈现功能，我们提供了用于处理某些特定于 Unity 的方面的专用方法。

## <a name="startup-and-shutdown"></a>启动和关闭

若要初始化远程呈现，请使用 `RemoteManagerUnity` 。 此类调入到泛型， `RemoteManager` 但已经实现了 Unity 特定的详细信息。 例如，Unity 使用特定的坐标系统。 调用时 `RemoteManagerUnity.Initialize` ，将设置适当的约定。 调用还要求提供 Unity 摄像机，此照相机应用于显示远程呈现的内容。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

对于关闭远程渲染，请调用 `RemoteManagerStatic.ShutdownRemoteRendering()` 。

`AzureSession`创建并选择作为主呈现会话之后，必须向注册它 `RemoteManagerUnity` ：

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完整的示例代码

下面的代码演示了在 Unity 中初始化 Azure 远程呈现所需的所有步骤：

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>便利性函数

### <a name="session-state-events"></a>会话状态事件

`RemoteManagerUnity.OnSessionUpdate` 在事件的会话状态发生更改时发出事件，有关详细信息，请参阅代码文档。

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` 是一个可选组件，用于简化设置和会话管理。 它包含一些选项，可用于在应用程序退出时自动停止其会话，或者在编辑器中退出播放模式，以及在需要时自动续订会话租约。 它会缓存数据（例如会话属性） (查看其 `LastProperties` 变量) ，并为会话状态更改和会话错误公开事件。

一次不能有一个以上的实例 `ARRServiceUnity` 。 它旨在让你快速入门，因为它实现了一些通用功能。 对于更大的应用程序，可能更愿意自己执行这些操作。

有关如何设置和使用的示例， `ARRServiceUnity` 请参阅 [教程：查看远程呈现的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)。

## <a name="next-steps"></a>后续步骤

* [安装 Unity 的远程渲染包](install-remote-rendering-unity-package.md)
* [教程：查看远程渲染的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)
