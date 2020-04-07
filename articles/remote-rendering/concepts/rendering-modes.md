---
title: 渲染模式
description: 描述不同的服务器端呈现模式
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681696"
---
# <a name="rendering-modes"></a>渲染模式

远程渲染提供了两种主要操作模式：**基于切片的合成**模式和**深度合成**模式。 这些模式确定工作负载如何分布在服务器上的多个 GPU 中。 必须在连接时指定该模式，并且在运行时无法更改。

这两种模式都有优势，但也具有固有的功能限制，因此选择最合适的模式是特定于用例的。

## <a name="modes"></a>模式

现在更详细地讨论了这两种模式。

### <a name="tilebasedcomposition-mode"></a>"基于"切片的合成"模式

在**基于磁贴的合成**模式下，每个涉及的 GPU 都会在屏幕上渲染特定的子矩形（切片）。 主 GPU 在以视频帧形式发送到客户端之前，从切片中组成最终图像。 因此，所有 GPU 都需要具有相同的资源进行渲染，因此加载的资产需要适合单个 GPU 的内存。

此模式下的渲染质量略好于**深度基合成**模式，因为 MSAA 可以针对每个 GPU 处理完整的几何集。 以下屏幕截图显示，防锯齿同样适用于两个边：

![基于切片的合成中的 MSAA](./media/service-render-mode-quality.png)

此外，在此模式下，每个部件都可以切换到透明材质，或通过[分层状态覆盖组件](../overview/features/override-hierarchical-state.md)切换到**透明**模式

### <a name="depthbasedcomposition-mode"></a>"深度组合"模式

在**深度基于的合成**模式下，每个涉及的 GPU 以全屏分辨率呈现，但仅呈现一个网件的子集。 主 GPU 上的最终图像合成可注意根据零件的深度信息正确合并部件。 当然，内存负载分布在 GPU 中，因此允许渲染不适合单个 GPU 内存的模型。

每个 GPU 都使用 MSAA 来抗锯齿本地内容。 但是，不同 GPU 的边之间可能存在固有的混叠。 通过后处理最终图像来缓解此效果，但 MSAA 质量仍比**基于磁贴的合成**模式差。

MSAA 工件图如下：![基于深度的 MSAA](./media/service-render-mode-balanced.png)

抗锯齿在雕塑和窗帘之间正常工作，因为这两个部分都在同一个 GPU 上渲染。 另一方面，窗帘和墙之间的边缘显示一些别名，因为这两个部分由不同的 GPU 组成。

此模式的最大限制是几何零件不能动态切换到透明材质，**而透明**模式也不能适用于[分层状态覆盖组件](../overview/features/override-hierarchical-state.md)。 但是，其他状态覆盖功能（轮廓、颜色色调、...）确实可以正常工作。 此外，在转换时标记为透明的材料在此模式下正常工作。

### <a name="performance"></a>性能

这两种模式的性能特性确实因用例而异，很难推理或提供一般性建议。 如果您不受上述限制（内存或透明度/查看）的限制，建议尝试这两种模式，并使用各种摄像机位置监控性能。

## <a name="setting-the-render-mode"></a>设置渲染模式

远程渲染 VM 上使用的渲染模式在`AzureSession.ConnectToRuntime`期间`ConnectToRuntimeParams`通过 指定。

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
* [分层状态覆盖组件](../overview/features/override-hierarchical-state.md)
