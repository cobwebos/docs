---
title: 对象边界
description: 解释如何查询空间对象边界
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681709"
---
# <a name="object-bounds"></a>对象边界

对象边界表示[实体](entities.md)及其子级占用的卷。 在 Azure 远程渲染中，对象边界始终作为*轴对齐的边界框*（AABB） 给出。 对象边界可以是*本地空间*，也可以位于*世界空间*。 无论哪种方式，它们始终与轴对齐，这意味着局部世界空间表示的范围和体积可能不同。

## <a name="querying-object-bounds"></a>查询对象边界

可以直接从网格资源查询[网格](meshes.md)的局部 AABB。 这些边界可以使用实体的转换转换为实体的本地空间或世界空间。

可以以这种方式计算整个对象层次结构的边界，但这需要遍历层次结构、查询每个网格的边界并手动组合它们。 此操作既繁琐又低效。

更好的方法是调用`QueryLocalBoundsAsync`实体或`QueryWorldBoundsAsync`调用实体。 然后，计算将卸载到服务器，以最小的延迟返回。

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
