---
title: 空间查询
description: 如何在场景中执行空间查询
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680526"
---
# <a name="spatial-queries"></a>空间查询

空间查询是一项操作，你可以使用这些操作来询问远程呈现服务哪些对象位于某个区域。 空间查询经常用于实现交互，如确定用户所指向的对象。

在服务器上计算所有空间查询。 因此，它们是异步操作，结果会随网络延迟而延迟。 由于每个空间查询都会生成网络流量，因此请注意不要一次执行过多的操作。

## <a name="collision-meshes"></a>冲突网格

空间查询由[Havok 物理](https://www.havok.com/products/havok-physics)引擎提供支持，并需要专用的冲突网格。 默认情况下，[模型转换](../../how-tos/conversion/model-conversion.md)会生成冲突网格。 如果不需要对复杂模型进行空间查询，请考虑在[转换选项](../../how-tos/conversion/configure-model-conversion.md)中禁用冲突网格生成，因为它会对多种方式产生影响：

* [模型转换](../../how-tos/conversion/model-conversion.md)需要较长的时间。
* 转换后的模型文件大小明显较大，会影响下载速度。
* 运行时加载时间较长。
* 运行时 CPU 内存消耗较高。
* 对于每个模型实例，都有很小的运行时性能开销。

## <a name="ray-casts"></a>Ray 转换

*Ray 强制转换*是一种空间查询，在该查询中，运行时将检查哪些对象与射线相交，从给定位置开始，指向某个方向。 作为一种优化方式，还给出了最长的射线距离，不能搜索太远的对象。

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

有三个命中收集模式：

* **最接近：** 在此模式下，只会报告最接近的命中。
* **任何：** 如果您想*知道某一射线是否会*命中任何内容，但又不在意确切的点击次数，则首选此模式。 此查询的计算成本要低得多，但也只有少量的应用程序。
* **全部：** 在此模式下，将报告沿射线的所有命中，并按距离进行排序。 不要使用此模式，除非你真正需要的不是第一个命中。 限制报告的命中数和`MaxHits`选项。

若要有选择性地排除对象，则可以使用[HierarchicalStateOverrideComponent](override-hierarchical-state.md)组件。

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>命中结果

Ray 转换查询的结果是一组命中。 如果未命中任何对象，则数组为空。

命中具有以下属性：

* **HitEntity：** 命中了哪个[实体](../../concepts/entities.md)。
* **SubPartId：**[MeshComponent](../../concepts/meshes.md)中命中了哪些*子网格*。 可用于在该点对`MeshComponent.UsedMaterials` [材料](../../concepts/materials.md)进行索引和查找。
* **HitPosition：** Ray 与对象相交的世界空间位置。
* **HitNormal：** 网格在相交位置的世界空间表面法线。
* **DistanceToHit：** 从射线起始位置到命中的距离。

## <a name="next-steps"></a>后续步骤

* [对象边界](../../concepts/object-bounds.md)
* [覆盖分层状态](override-hierarchical-state.md)
