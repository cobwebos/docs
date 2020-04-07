---
title: 空间查询
description: 如何在场景中执行空间查询
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680526"
---
# <a name="spatial-queries"></a>空间查询

空间查询是可以询问远程呈现服务位于区域中的对象的操作。 空间查询通常用于实现交互，例如确定用户指向哪个对象。

所有空间查询都在服务器上计算。 因此，它们是异步操作，结果将延迟到达，具体取决于您的网络延迟。 由于每个空间查询都会生成网络流量，因此请注意不要一次执行太多操作。

## <a name="collision-meshes"></a>碰撞模因

空间查询由[Havok 物理](https://www.havok.com/products/havok-physics)引擎提供动力，需要存在专用的碰撞网格。 默认情况下，[模型转换](../../how-tos/conversion/model-conversion.md)生成冲突模样。 如果不需要对复杂模型进行空间查询，请考虑在[转换选项](../../how-tos/conversion/configure-model-conversion.md)中禁用冲突网格生成，因为它具有多种方式的影响：

* [模型转换](../../how-tos/conversion/model-conversion.md)将需要更长的时间。
* 转换后的模型文件大小明显较大，影响下载速度。
* 运行时加载时间较长。
* 运行时 CPU 内存消耗较高。
* 每个模型实例都有轻微的运行时性能开销。

## <a name="ray-casts"></a>雷铸

*光线强制转换*是一种空间查询，运行时检查哪些对象由光线相交，从给定位置开始并指向特定方向。 作为优化，还给出了最大光线距离，以便不搜索太远的对象。

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

有三种命中收集模式：

* **最近：** 在此模式下，仅报告最接近的命中。
* **任何：** 首选这种模式，当你想知道的是，*光线是否会*击中任何东西，但不在乎什么击中究竟。 此查询可以大大便宜评估，但也只有很少的应用程序。
* **所有：** 在此模式下，沿光线的所有命中都报告，按距离排序。 不要使用此模式，除非您确实需要超过第一次命中。 使用`MaxHits`选项限制报告命中数。

为了有选择地排除对象考虑光线强制转换，可以使用[分层状态覆盖组件组件](override-hierarchical-state.md)。

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>命中结果

光线转换查询的结果是命中数组。 如果未命中任何对象，则数组为空。

命中具有以下属性：

* **命中实体：** 哪个[实体](../../concepts/entities.md)被击中。
* **子部分Id：** 哪个*子网格*在[网格组件](../../concepts/meshes.md)中被击中。 可用于索引`MeshComponent.UsedMaterials`和查找[材料](../../concepts/materials.md)。
* **命中位置：** 光线与物体相交的世界空间位置。
* **命中正常值：** 网格在交点位置的世界空间表面正常。
* **距离命中：** 从光线起始位置到命中的距离。

## <a name="next-steps"></a>后续步骤

* [对象边界](../../concepts/object-bounds.md)
* [重写分层状态](override-hierarchical-state.md)
