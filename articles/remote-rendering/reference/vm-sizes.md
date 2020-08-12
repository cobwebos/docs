---
title: VM 大小
description: 描述可分配的不同 VM 大小
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: e8e439a055b71ed291573965c561ee31610e3ed4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121606"
---
# <a name="vm-sizes"></a>VM 大小

呈现服务可以在 Azure 中的两种不同类型的计算机上运行，称为 `Standard` 和 `Premium` 。

## <a name="polygon-limits"></a>多边形限制

VM 大小的硬限制为**20000000 个多边形** `Standard` 。 大小没有此类限制 `Premium` 。

当标准 VM 上的呈现器达到此限制时，它将呈现为棋盘背景：

![棋盘](media/checkerboard.png)

## <a name="allocate-the-vm"></a>分配 VM

在呈现会话初始化时必须指定所需的 VM 类型。 无法在正在运行的会话中更改它。 下面的代码示例显示了必须指定 VM 大小的位置：

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

对于[示例 PowerShell 脚本](../samples/powershell-example-scripts.md)，必须在该文件内指定 VM 大小 `arrconfig.json` ：

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
* 使用[层次结构状态重写组件](../overview/features/override-hierarchical-state.md)切换到不可见的模型或模型部件。

因此，可以编写一个应用程序，该应用程序的目标是将 `standard` 加载多个模型的多个模型，其中多边形计数接近于每个单个模型的限制。 当应用程序一次只显示一个模型时，不会触发棋盘。

### <a name="how-to-determine-the-number-of-polygons"></a>如何确定多边形的数目

可以通过两种方法来确定模型或场景中构成大小 VM 预算限制的多边形数量 `standard` ：
* 在模型转换端检索[转换输出 json 文件](../how-tos/conversion/get-information.md)，然后检查 `numFaces` [ *inputStatistics*节](../how-tos/conversion/get-information.md#the-inputstatistics-section)中的条目。
* 如果你的应用程序处理的是动态内容，则在运行时可以动态查询呈现的多边形的数目。 使用[性能评估查询](../overview/features/performance-queries.md#performance-assessment-queries)并 `polygonsRendered` 在结构中检查成员 `FrameStatistics` 。 `polygonsRendered` `bad` 当呈现器达到多边形限制时，该字段将设置为。 棋盘背景始终是褪色的，并有一些延迟，以确保在执行此异步查询后可以执行用户操作。 例如，用户操作可以隐藏或删除模型实例。

## <a name="pricing"></a>定价

有关每种类型的 VM 的定价详细细目，请参阅[远程呈现定价](https://azure.microsoft.com/pricing/details/remote-rendering)页。

## <a name="next-steps"></a>后续步骤
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
* [模型转换](../how-tos/conversion/model-conversion.md)

