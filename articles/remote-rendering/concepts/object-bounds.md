---
title: 对象边界
description: 说明如何查询空间对象边界
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681709"
---
# <a name="object-bounds"></a>对象边界

对象边界表示实体及其子[实体](entities.md)占用的卷。 在 Azure 远程呈现中，始终将对象边界指定为*轴对齐的边界框*（AABB）。 对象边界可以在*本地空间*中或在*世界空间*内。 无论采用哪种方式，它们始终是轴对齐的，这意味着在本地和世界空间表示形式之间，区和卷可能不同。

## <a name="querying-object-bounds"></a>查询对象边界

可以直接从网格资源查询[网格](meshes.md)的本地 AABB。 可以使用实体的转换将这些界限转换为实体的本地空间或世界空间。

可以通过这种方式计算整个对象层次结构的界限，但这需要遍历层次结构，查询每个网格的边界，并手动组合它们。 此操作既繁琐又低效。

更好的方法是在`QueryLocalBoundsAsync`实体`QueryWorldBoundsAsync`上调用或。 然后，将计算卸载到服务器并返回，延迟时间最短。

``` cs
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

## <a name="next-steps"></a>后续步骤

* [空间查询](../overview/features/spatial-queries.md)
