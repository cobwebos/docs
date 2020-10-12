---
title: 剪切平面
description: 介绍了什么是裁切平面，以及如何使用裁切平面
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 468d21abc861e905472d1d15405b1c8ba9e5be74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904872"
---
# <a name="cut-planes"></a>剪切平面

裁切平面是一项视觉功能，它会剪裁虚拟平面一端上的像素，从而呈现[网格](../../concepts/meshes.md)的内部。
下图演示了该效果。 左侧显示了原始网格，在右侧可以看到网格内部：

![裁切平面](./media/cutplane-1.png)

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
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 属性

裁切平面组件上公开了以下属性：

* `Enabled`：可以通过禁用此组件来暂时关闭裁切平面。 已禁用的裁切平面不会产生渲染开销，且不会计入全局裁切平面限制。

* `Normal`：指定要将哪个方向（+X、-X、+Y、-Y、+Z、-Z）用作平面法线。 此方向相对于所有者实体的方向。 请移动和旋转所有者实体以便精确定位。

* `FadeColor` 和 `FadeLength`：

  如果 *FadeColor* 的 alpha 值不为零，则靠近裁切平面的像素将会根据 FadeColor 的 RGB 部分淡化。 alpha 通道的强度决定了像素是根据淡化颜色完全淡化，还是仅部分淡化。 *FadeLength* 定义超过什么距离将进行这种淡化。

* `ObjectFilterMask`：一个筛选器位掩码，它确定哪个几何图形受切口平面的影响。 有关详细信息，请参阅下一段。

### <a name="selective-cut-planes"></a>选择性剪切平面

可以配置单个切削平面，使其仅影响特定几何。 下图说明了此设置的实际工作原理：

![选择性剪切平面](./media/selective-cut-planes.png)

筛选将通过在 "切割" 平面端上的位掩码与在几何图形上设置的另一个位掩码之间进行 **逻辑位掩码比较** 。 如果掩码之间的逻辑运算的结果 `AND` 不为零，则该切口平面将影响几何。

* 切割平面组件的位掩码通过其 `ObjectFilterMask` 属性设置
* 几何子层次结构上的位掩码是通过[HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)设置的。

示例：

| 剪切平面筛选器掩码 | 几何筛选器掩码  | 逻辑的结果 `AND` | 切口平面会影响几何？  |
|--------------------|-------------------|-------------------|:----------------------------:|
|  (0000 0001) = = 1   |  (0000 0001) = = 1  |  (0000 0001) = = 1  | 是 |
|  (1111 0000) = = 240 |  (0001 0001) = = 17 |  (0001 0000) = = 16 | 是 |
|  (0000 0001) = = 1   |  (0000 0010) = = 2  |  (0000 0000) = = 0  | 否 |
|  (0000 0011) = = 3   |  (0000 1000) = = 8  |  (0000 0000) = = 0  | 否 |

>[!TIP]
> 将 "切口" 平面设置 `ObjectFilterMask` 为0意味着它不会影响任何几何图形，因为逻辑的结果 `AND` 永远不能为 null。 渲染系统最初不会考虑这些平面，因此这是一种禁用各个切削平面的轻型方法。 这些切口平面也不会计算出8个活动平面的限制。

## <a name="limitations"></a>限制

* Azure 远程呈现 **最多支持八个活动切削平面**。 你可以创建更多的裁切平面组件，但如果尝试同时启用更多组件，它将忽略激活操作。 如果要切换哪些组件会影响场景，请先禁用其他平面。
* 剪切平面是一项纯粹的视觉特征，它们不会影响 [空间查询](spatial-queries.md)的结果。 如果你确实想要将光线投影到裁开的网格，可以调整光线的起点，使其位于裁切平面上。 这样，光线便只会落在可视部分上。

## <a name="performance-considerations"></a>性能注意事项

在渲染期间，每个活动的裁切平面会产生较小的成本。 请禁用或删除不再需要的裁切平面。

## <a name="api-documentation"></a>API 文档

* [C # CutPlaneComponent 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C + + CutPlaneComponent 类](https://docs.microsoft.com/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>后续步骤

* [单面渲染](single-sided-rendering.md)
* [空间查询](spatial-queries.md)
