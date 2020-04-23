---
title: 调试渲染
description: 服务器端调试呈现效果概述
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868162"
---
# <a name="debug-rendering"></a>调试渲染

调试呈现 API 提供了一系列全局选项，用于使用不同的调试效果来更改服务器端呈现。

## <a name="available-debug-rendering-effects"></a>可用的调试呈现效果

|设置                          | 效果                               |
|---------------------------------|:-------------------------------------|
|框架计数器                    | 将文本叠加渲染到框架的左上角。 文本显示当前服务器端帧 ID，该 ID 随着呈现过程而不断递增。 |
|多边形计数                    | 将文本叠加渲染到框架的左上角。 文本显示当前呈现的多边形量，与[服务器端性能查询](performance-queries.md)查询的值相同| 
|线框                        | 如果启用，则服务器上加载的所有对象几何体都将在线框模式下呈现。 在此模式下，只有多边形的边缘才会栅格。 |

以下代码支持以下调试效果：

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![调试渲染](./media/debug-rendering.png)

> [!NOTE]
> 所有调试呈现效果都是影响整个帧的全局设置。

## <a name="use-cases"></a>用例

调试呈现 API 用于简单的调试任务，例如验证服务连接实际正常运行。 文本呈现选项直接影响向的视频帧。 启用它们可验证是否接收了新帧并正确解码了视频。

但是，所提供的效果不会对服务运行状况进行任何详细的反省。 建议为此用例进行[服务器端性能查询](performance-queries.md)。

## <a name="performance-considerations"></a>性能注意事项

* 启用文本叠加会产生很少或根本没有性能开销。
* 启用线框模式确实会产生非同寻常的性能开销，但可能因场景而异。 对于复杂场景，此模式可能会导致帧速率降至 60 Hz 目标以下。

## <a name="next-steps"></a>后续步骤

* [渲染模式](../../concepts/rendering-modes.md)
* [服务器端性能查询](performance-queries.md)
