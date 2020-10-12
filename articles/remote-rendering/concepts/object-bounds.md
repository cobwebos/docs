---
title: 对象边界
description: 说明如何查询空间对象边界
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: bd2b1026f69b5ec5814124a92d6a78d7f0225143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613813"
---
# <a name="object-bounds"></a>对象边界

对象边界表示[实体](entities.md)及其子对象占用的空间量。 在 Azure 远程渲染中，始终将对象边界指定为轴对齐边界框 (AABB)。 对象边界可以位于局部空间或世界空间中。 无论采用哪种方式，它们始终是轴对齐的，这意味着在局部空间和世界空间表示形式之间，盘区和卷可能有所不同。

## <a name="querying-object-bounds"></a>查询对象边界

可以直接从网格资源查询[网格](meshes.md)的本地 AABB。 可以使用实体的转换将这些边界转换为实体的局部空间或世界空间。

可以通过这种方式计算整个对象层次结构的边界，但这需要遍历层次结构，查询每个网格的边界，并手动组合它们。 此操作既繁琐又低效。

更好的方法是在实体上调用 `QueryLocalBoundsAsync` 或 `QueryWorldBoundsAsync`。 然后，计算将卸载到服务器并返回，延迟时间最短。

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="api-documentation"></a>API 文档

* [C # QueryLocalBoundsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C + + Entity：： QueryLocalBoundsAsync](https://docs.microsoft.com/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>后续步骤

* [空间查询](../overview/features/spatial-queries.md)
