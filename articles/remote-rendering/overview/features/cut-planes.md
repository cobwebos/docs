---
title: 剪切平面
description: 介绍了什么是裁切平面，以及如何使用裁切平面
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 7adf9a9701eb2492f0b13a26af1dbaf8de631373
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021358"
---
# <a name="cut-planes"></a>剪切平面

裁切平面是一项视觉功能，它会剪裁虚拟平面一端上的像素，从而呈现[网格](../../concepts/meshes.md)的内部。
下图演示了该效果。 左侧显示了原始网格，在右侧可以看到网格内部：

![裁切平面](./media/cutplane-1.png)

## <a name="limitations"></a>限制

* 目前，Azure 远程渲染**最多支持 8 个活动的裁切平面**。 你可以创建更多的裁切平面组件，但如果尝试同时启用更多组件，它将忽略激活操作。 如果你要切换应当影响场景的组件，请先禁用其他平面。
* 每个裁切平面都会影响所有远程渲染的对象。 目前没有任何办法可以排除特定的对象或网格部分。
* 裁切平面只是一个视觉功能，不会影响[空间查询](spatial-queries.md)的结果。 如果你确实想要将光线投影到裁开的网格，可以调整光线的起点，使其位于裁切平面上。 这样，光线便只会落在可视部分上。

## <a name="performance-considerations"></a>性能注意事项

在渲染期间，每个活动的裁切平面会产生较小的成本。 请禁用或删除不再需要的裁切平面。

## <a name="cutplanecomponent"></a>CutPlaneComponent

可以通过创建 *CutPlaneComponent* 在场景中添加裁切平面。 平面的位置和方向由组件的所有者[实体](../../concepts/entities.md)确定。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 属性

裁切平面组件上公开了以下属性：

* `Enabled`：可以通过禁用此组件来暂时关闭裁切平面。 已禁用的裁切平面不会产生渲染开销，且不会计入全局裁切平面限制。

* `Normal`：指定要将哪个方向（+X、-X、+Y、-Y、+Z、-Z）用作平面法线。 此方向相对于所有者实体的方向。 请移动和旋转所有者实体以便精确定位。

* `FadeColor` 和 `FadeLength`：

  如果 *FadeColor* 的 alpha 值不为零，则靠近裁切平面的像素将会根据 FadeColor 的 RGB 部分淡化。 alpha 通道的强度决定了像素是根据淡化颜色完全淡化，还是仅部分淡化。 *FadeLength* 定义超过什么距离将进行这种淡化。

## <a name="next-steps"></a>后续步骤

* [单面渲染](single-sided-rendering.md)
* [空间查询](spatial-queries.md)
