---
title: 为统一设置远程渲染
description: 如何在 Unity 项目中初始化 Azure 远程呈现
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681137"
---
# <a name="set-up-remote-rendering-for-unity"></a>为统一设置远程渲染

为了在 Unity 中启用 Azure 远程呈现 （ARR），我们提供专门的方法，用于处理一些特定于 Unity 的方面。

## <a name="startup-and-shutdown"></a>启动和关闭

要初始化远程渲染，`RemoteManagerUnity`请使用 。 此类调用泛型`RemoteManager`，但已为您实现特定于 Unity 的详细信息。 例如，Unity 使用特定的坐标系。 调用`RemoteManagerUnity.Initialize`时，将设置适当的约定。 该呼叫还要求您提供 Unity 摄像机，该摄像机应用于显示远程呈现的内容。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

对于关闭远程渲染，请调用`RemoteManagerStatic.ShutdownRemoteRendering()`。

创建`AzureSession`并选择 作为主呈现会话后，必须将其注册到`RemoteManagerUnity`：

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完整示例代码

以下代码演示了在 Unity 中初始化 Azure 远程呈现所需的所有步骤：

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

## <a name="convenience-functions"></a>便利功能

### <a name="session-state-events"></a>会话状态事件

`RemoteManagerUnity.OnSessionUpdate`发出会话状态更改时的事件，有关详细信息，请参阅代码文档。

### <a name="arrserviceunity"></a>ARR服务团结

`ARRServiceUnity`是简化设置和会话管理的可选组件。 它包含在编辑器中退出应用程序或播放模式时自动停止其会话的选项，以及在需要时自动续订会话租约。 它缓存会话属性等数据（请参阅其`LastProperties`变量），并公开会话状态更改和会话错误的事件。

一次不能有多个实例`ARRServiceUnity`。 它旨在通过实现一些常见功能来加快您入门速度。 然而，对于一个更大的应用程序，最好自己做这些事情。

有关如何设置和使用`ARRServiceUnity`的示例，请参阅[教程：从头开始设置 Unity 项目](../../tutorials/unity/project-setup.md)。

## <a name="next-steps"></a>后续步骤

* [安装远程呈现包以进行统一](install-remote-rendering-unity-package.md)
* [教程：从头开始设置 Unity 项目](../../tutorials/unity/project-setup.md)
