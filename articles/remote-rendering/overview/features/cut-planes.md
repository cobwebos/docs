---
title: 剪切平面
description: 说明什么是切削平面以及如何使用它们
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681020"
---
# <a name="cut-planes"></a>剪切平面

*切割平面*是一种视觉功能，它在虚拟平面的一侧剪辑像素，并显示[网格](../../concepts/meshes.md)内部。
下图演示了该效果。 左侧显示了可在网格内查看的原始网格：

![切割平面](./media/cutplane-1.png)

## <a name="limitations"></a>限制

* 目前，Azure 远程渲染**最多支持八个活动切削平面**。 你可以创建更多的 "剪切" 平面组件，但如果尝试同时启用，则会忽略激活。 如果要切换哪个组件会影响场景，请先禁用其他平面。
* 每个切割平面都将影响所有远程呈现的对象。 目前没有办法排除特定的对象或网格部分。
* 剪切平面只是一项视觉特征，它们不会影响[空间查询](spatial-queries.md)的结果。 如果您确实想要将光线转换为剪切打开的网格，则可以调整射线的起点，使其位于切削平面上。 这样一来，ray 只能命中可见部分。

## <a name="performance-considerations"></a>性能注意事项

在呈现期间，每个活动的切削面都会产生较小的成本。 禁用或删除不需要的 "剪切" 平面。

## <a name="cutplanecomponent"></a>CutPlaneComponent

您可以通过创建*CutPlaneComponent*向场景中添加一个切口平面。 平面的位置和方向由组件的所有者[实体](../../concepts/entities.md)确定。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 属性

以下属性在切口平面组件上公开：

* **已启用：** 您可以通过禁用组件来暂时关闭切削平面。 禁用的 "剪切" 平面不会产生渲染开销，也不会根据全局切削平面限制进行计数。

* **正常：** 指定将哪个方向（+ X，-X，+ Y，-Y，+ Z，-Z）用作平面法线。 此方向相对于所有者实体的方向。 移动和旋转所有者实体以获得精确位置。

* **FadeColor**和**FadeLength：**

  如果*FadeColor*的 alpha 值为非零，则接近切削平面的像素将淡入 FADECOLOR 的 RGB 部分。 Alpha 通道的强度决定了它是完全淡入淡化色还是仅部分淡出。 *FadeLength*定义此淡化将发生的距离。

## <a name="next-steps"></a>后续步骤

* [单面呈现](single-sided-rendering.md)
* [空间查询](spatial-queries.md)
