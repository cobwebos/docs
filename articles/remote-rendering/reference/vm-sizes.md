---
title: 服务器大小
description: 描述可分配的不同服务器大小
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: b37aabb39e19fa5ec53d2b006a7cbc1793adad72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90988043"
---
# <a name="server-sizes"></a>服务器大小

Azure 远程呈现提供两种服务器配置： `Standard` 和 `Premium` 。

## <a name="polygon-limits"></a>多边形限制

使用大小服务器的远程呈现 `Standard` 最大场景大小为20000000个多边形。 大小为的远程呈现 `Premium` 不会强制执行硬限制，但如果内容超出服务的呈现功能，性能可能会下降。

当 "标准" 服务器上的呈现器达到此限制时，它将呈现为棋盘背景：

![屏幕截图使用 "工具" 菜单显示黑色和白色正方形的网格。](media/checkerboard.png)

## <a name="specify-the-server-size"></a>指定服务器大小

在呈现会话初始化时必须指定所需的服务器配置类型。 无法在正在运行的会话中更改它。 下面的代码示例显示了必须指定服务器大小的位置：

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

对于 [示例 PowerShell 脚本](../samples/powershell-example-scripts.md)，必须在文件中指定所需的服务器大小 `arrconfig.json` ：

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>呈现器如何计算多边形的数目

考虑的限制测试的多边形数是实际传递到呈现器的多边形的数目。 此几何图形通常是所有实例化模型的总和，但也有异常。 **不包括**以下几何图形：
* 已加载在视图上完全相同的模型实例。
* 使用 [层次结构状态重写组件](../overview/features/override-hierarchical-state.md)切换到不可见的模型或模型部件。

因此，可以编写一个应用程序，该应用程序的目标是将 `standard` 加载多个模型的多个模型，其中多边形计数接近于每个单个模型的限制。 当应用程序一次只显示一个模型时，不会触发棋盘。

### <a name="how-to-determine-the-number-of-polygons"></a>如何确定多边形的数目

可以通过两种方法来确定模型或场景中构成配置大小预算限制的多边形的数目 `standard` ：
* 在模型转换端检索[转换输出 json 文件](../how-tos/conversion/get-information.md)，然后检查 `numFaces` [ *inputStatistics*节](../how-tos/conversion/get-information.md#the-inputstatistics-section)中的条目。
* 如果你的应用程序处理的是动态内容，则在运行时可以动态查询呈现的多边形的数目。 使用 [性能评估查询](../overview/features/performance-queries.md#performance-assessment-queries) 并 `polygonsRendered` 在结构中检查成员 `FrameStatistics` 。 `polygonsRendered` `bad` 当呈现器达到多边形限制时，该字段将设置为。 棋盘背景始终是褪色的，并有一些延迟，以确保在执行此异步查询后可以执行用户操作。 例如，用户操作可以隐藏或删除模型实例。

## <a name="pricing"></a>定价

若要详细了解每种类型的配置，请参阅 [远程呈现定价](https://azure.microsoft.com/pricing/details/remote-rendering) 页。

## <a name="next-steps"></a>后续步骤
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
* [模型转换](../how-tos/conversion/model-conversion.md)

