---
title: 空间查询
description: 如何在场景中执行空间查询
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 64350ca7213ce13e5d80daf43e74995d14db4061
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892722"
---
# <a name="spatial-queries"></a>空间查询

空间查询是一种操作，可用来询问远程渲染服务哪些对象位于一个区域中。 空间查询经常用于实现交互，如确定用户正在指向哪个对象。

所有空间查询都是在服务器上进行计算。 因此，空间查询是异步操作，查询结果会有延迟，具体视网络延迟而定。 由于每个空间查询都会生成网络流量，因此请注意不要一次执行过多空间查询。

## <a name="collision-meshes"></a>冲突网格

空间查询由 [Havok Physics](https://www.havok.com/products/havok-physics) 引擎提供技术支持，并且需要有一个专用的冲突网格。 默认情况下，[模型转换](../../how-tos/conversion/model-conversion.md)生成冲突网格。 如果在复杂模型上不需要空间查询，不妨在[转换选项](../../how-tos/conversion/configure-model-conversion.md)中禁用冲突网格生成，因为它会以多种方式产生影响：

* [模型转换](../../how-tos/conversion/model-conversion.md)的耗时大大延长。
* 转换后的模型文件大小明显变大，同时影响下载速度。
* 运行时加载时间延长。
* 运行时 CPU 内存消耗变大。
* 对于每个模型实例，都有少量的运行时性能开销。

## <a name="ray-casts"></a>光线投射

光线投射是一种空间查询，即运行时检查哪些对象与光线相交，从给定的位置起始，并指向特定的方向。 作为一种优化，还给出了最长光线距离，以免搜索太远的对象。

```cs
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<AzureSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = { 0, 0, 0 };
    rayCast.EndPos = { 0, 0, 1 };
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    ApiHandle<RaycastQueryAsync> castQuery = *session->Actions()->RayCastQueryAsync(rayCast);

    castQuery->Completed([](const ApiHandle<RaycastQueryAsync>& async)
        {
            std::vector<RayCastHit> hits;
            async->GetResult(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        });
}
```


有三种命中收集模式：

* **`Closest`：** 在这种模式下，只报告最靠近的命中。
* **`Any`：** 如果只想了解光线是否命中任何东西，但不关心具体命中了什么，最好选择这种模式。 此查询的计算成本可能会大大降低，但几乎没有应用程序。
* **`All`：** 在这种模式下，将报告所有沿光线的命中，并按距离进行排序。 除非确实除了第一次命中还需要更多命中，否则请不要使用这种模式。 使用 `MaxHits` 选项可以限制报告的命中数。

若要有选择性地从光线投射的目标对象范围内排除对象，可以使用 [HierarchicalStateOverrideComponent](override-hierarchical-state.md) 组件。

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>命中结果

光线投射查询的结果是命中数组。 如果未命中任何对象，数组就是空的。

命中有以下属性：

* **`HitEntity`：** 命中了哪个[实体](../../concepts/entities.md)。
* **`SubPartId`：** 在 *MeshComponent* 中命中了哪个[子网格](../../concepts/meshes.md)。 可用于索引到 `MeshComponent.UsedMaterials`，并查找此时的[材料](../../concepts/materials.md)。
* **`HitPosition`：** 光线与对象相交的自然空间位置。
* **`HitNormal`：** 相交位置处的网格的自然空间曲面法线。
* **`DistanceToHit`：** 从光线起始位置到命中的距离。

## <a name="next-steps"></a>后续步骤

* [对象边界](../../concepts/object-bounds.md)
* [替代分层状态](override-hierarchical-state.md)
