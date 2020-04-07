---
title: 切割平面
description: 解释什么是切割平面以及如何使用它们
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681020"
---
# <a name="cut-planes"></a>切割平面

*剪切平面*是一种视觉特征，用于剪辑虚拟平面一侧的像素，揭示[网](../../concepts/meshes.md)内。
下图演示了效果。 左侧显示原始网格，右侧可以查看网格内部：

![切割平面](./media/cutplane-1.png)

## <a name="limitations"></a>限制

* 目前，Azure 远程呈现最多支持八个**活动切割平面**。 您可以创建更多切割平面组件，但如果尝试同时启用更多，它将忽略激活。 如果要切换要影响场景的组件，请先禁用其他平面。
* 每个切割平面都会影响所有远程渲染的对象。 目前无法排除特定对象或网格部分。
* 剪切平面纯粹是视觉特征，它们不会影响[空间查询](spatial-queries.md)的结果。 如果确实希望将铸入剪切开放网格的光线，则可以调整光线的起始点以位于切割平面上。 这样，光线只能击中可见部分。

## <a name="performance-considerations"></a>性能注意事项

每个活动切割平面在渲染过程中会产生少量成本。 在不需要切割平面时禁用或删除它们。

## <a name="cutplanecomponent"></a>切割平面组件

通过创建*CutPlane 组件*，向场景添加剪切平面。 平面的位置和方向由组件的所有者[实体](../../concepts/entities.md)决定。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>切割平面组件属性

以下属性在切割平面组件上公开：

* **已启用：** 您可以通过禁用组件暂时关闭切割平面。 禁用的切割平面不会产生渲染开销，也不会计入全局切割平面限制。

* **正常：** 指定使用哪个方向（+X、-X、+Y、Y、+Z、-Z）作为平面法线。 此方向与所有者实体的方向相关。 移动和旋转所有者实体以进行精确放置。

* **淡入淡出颜色**和**淡入淡出长度：**

  如果*淡入淡出颜色*的 alpha 值是非零，则靠近剪切平面的像素将淡入淡入 FadeColor 的 RGB 部分。 Alpha 通道的强度决定了它是完全褪色还是仅部分淡入淡出。 *淡入淡出符定义*此淡入淡出将发生的距离。

## <a name="next-steps"></a>后续步骤

* [单面渲染](single-sided-rendering.md)
* [空间查询](spatial-queries.md)
