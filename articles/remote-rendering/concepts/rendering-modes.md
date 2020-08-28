---
title: 渲染模式
description: 描述不同的服务器端呈现模式
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d7a895f3b565fdd4ec4659045034d0200355a60
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021861"
---
# <a name="rendering-modes"></a>渲染模式

远程呈现提供两种主要的操作模式： **TileBasedComposition** 模式和 **DepthBasedComposition** 模式。 这些模式决定了如何在服务器上的多个 Gpu 之间分布工作负荷。 此模式必须在连接时指定，并且在运行时无法更改。

这两种模式都具有优势，但也具有固有的功能限制，因此选择最适合的模式是使用方案特定的。

## <a name="modes"></a>模式

现在更详细地讨论了这两种模式。

### <a name="tilebasedcomposition-mode"></a>\ "TileBasedComposition" 模式

在 **TileBasedComposition** 模式下，每个涉及的 GPU 都将) 屏幕上的特定 subrectangles (图块。 主 GPU 在将磁贴作为视频帧发送到客户端之前，从磁贴中编写最终映像。 相应地，所有 Gpu 都需要有一组相同的资源用于呈现，因此，已加载的资产需要容纳到一个 GPU 的内存中。

在此模式下，呈现质量比在 **DepthBasedComposition** 模式下稍微好些，因为 MSAA 可对每个 GPU 使用整套几何。 以下屏幕截图显示了这两种边缘的抗锯齿操作：

![TileBasedComposition 中的 MSAA](./media/service-render-mode-quality.png)

此外，在此模式下，每个部分都可以切换到透明材料，或通过[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)切换到**查看**模式。

### <a name="depthbasedcomposition-mode"></a>\ "DepthBasedComposition" 模式

在 **DepthBasedComposition** 模式下，每个涉及的 GPU 都以全屏分辨率呈现，而只呈现网格的一部分。 主 GPU 上的最终映像组合会注意到部分按照其深度信息正确地进行了合并。 通常情况下，内存负载分布在 Gpu 上，因此允许呈现无法装入单个 GPU 内存的模型。

每个一个 GPU 都使用 MSAA 来消除本地内容。 但是，来自不同 Gpu 的边缘之间可能存在固有的别名。 通过后处理最终映像，但 MSAA 质量仍比在 **TileBasedComposition** 模式下更糟，这种效果会得到缓解。

下图演示了 MSAA 项目： ![ DepthBasedComposition 中的 msaa](./media/service-render-mode-balanced.png)

消除锯齿在 sculpture 和窗帘之间正常工作，因为这两个部分都呈现在同一 GPU 上。 另一方面，窗帘和墙壁之间的边缘会显示一些别名，因为这两个部分是由不同 Gpu 组成的。

此模式的最大限制是，几何图形部分不能动态切换为透明材料，也不能对[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)使用 "**查看**" 模式。 不过，其他状态覆盖功能 (轮廓、颜色色调 ) 确实有效。 此外，在转换时标记为透明的材料在此模式下可以正常工作。

### <a name="performance"></a>性能

这两种模式的性能特征根据用例的不同而不同，因此很难或提供一般建议。 如果你不受上述限制 (内存或透明度/) 的限制，则建议尝试这两种模式并使用各种相机位置来监视性能。

## <a name="setting-the-render-mode"></a>设置呈现模式

远程呈现服务器上使用的呈现模式是通过在期间指定的 `AzureSession.ConnectToRuntime` `ConnectToRuntimeParams` 。

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>后续步骤

* [会话](../concepts/sessions.md)
* [分层状态重写组件](../overview/features/override-hierarchical-state.md)
