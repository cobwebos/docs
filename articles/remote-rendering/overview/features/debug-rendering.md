---
title: 调试渲染
description: 服务器端调试渲染效果概述
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868162"
---
# <a name="debug-rendering"></a>调试渲染

调试呈现 API 提供了一系列全局选项，用不同的调试效果更改服务器端呈现。

## <a name="available-debug-rendering-effects"></a>可用调试渲染效果

|设置                          | 效果                               |
|---------------------------------|:-------------------------------------|
|帧计数器                    | 将文本覆盖呈现在框架的左上角。 此文本显示当前服务器端帧 ID，该 ID 随着呈现过程的不断递增。 |
|多边形计数                    | 将文本覆盖呈现在框架的左上角。 此文本显示当前呈现的多边形数量，与[服务器端性能查询](performance-queries.md)所查询的值相同的值| 
|线框                        | 如果启用，则在服务器上加载的所有对象几何都将以线框模式呈现。 在此模式下，只会对多边形边缘进行栅格化。 |

以下代码启用以下调试效果：

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

![调试呈现](./media/debug-rendering.png)

> [!NOTE]
> 所有调试渲染效果都是影响整个帧的全局设置。

## <a name="use-cases"></a>用例

调试呈现 API 用于简单的调试任务，如验证服务连接实际启动并正常运行。 文本呈现选项直接影响下流视频帧。 启用它们将验证是否接收了新帧并正确地对视频进行了解码。

不过，提供的效果不会对服务运行状况提供任何详细的自检。 对于此用例，建议使用[服务器端性能查询](performance-queries.md)。

## <a name="performance-considerations"></a>性能注意事项

* 启用文本覆盖几乎不会产生性能开销。
* 启用线框模式确实会产生不明显的性能开销，但这可能因场景而异。 对于复杂的场景，此模式可能会导致帧速率下降到 60-Hz 目标以下。

## <a name="next-steps"></a>后续步骤

* [渲染模式](../../concepts/rendering-modes.md)
* [服务器端性能查询](performance-queries.md)
