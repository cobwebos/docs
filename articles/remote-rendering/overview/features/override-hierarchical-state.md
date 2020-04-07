---
title: 分层状态覆盖
description: 解释分层状态覆盖组件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680786"
---
# <a name="hierarchical-state-override"></a>分层状态覆盖

在许多情况下，有必要动态更改[模型](../../concepts/models.md)各部分的外观，例如隐藏子图或将部分切换到透明渲染。 更改每个所涉及的部件的材料并不实际，因为它需要遍过整个场景图，并管理每个节点上的材料克隆和分配。

要以尽可能少的开销完成此用例，请使用 。 `HierarchicalStateOverrideComponent` 此组件在场景图的任意分支上实现分层状态更新。 这意味着，可以在场景图中的任何级别上定义状态，并逐渐滴入层次结构，直到它被新状态覆盖或应用于叶对象。

例如，考虑汽车的型号，并且您想要将整辆车切换为透明，但发动机内部部件除外。 此用例仅涉及组件的两个实例：

* 第一个组件被分配到模型的根节点，并打开整辆车的透明渲染。
* 第二个组件分配给引擎的根节点，并通过显式关闭通路模式再次覆盖状态。

## <a name="features"></a>功能

可以重写的固定状态集是：

* **隐藏**： 场景图中的相应模分被隐藏或显示。
* **色调**：渲染的对象可以着色，其单独的色调和色调权重。 下图显示了对车轮边缘着色的颜色。
  
  ![颜色色调](./media/color-tint.png)

* **透明**：几何体以半透明方式呈现，例如显示对象的内部部分。 下图显示了整个车辆在看通模式下呈现，红色制动卡钳除外：

  ![查看](./media/see-through.png)

  > [!IMPORTANT]
  > 仅当使用*基于"基于切片的合成"*[呈现模式](../../concepts/rendering-modes.md)时，透出效果才起作用。

* **选定**： 几何体使用[选择轮廓](outlines.md)呈现。

  ![选择大纲](./media/selection-outline.png)

* **禁用碰撞**： 几何体不受[空间查询。](spatial-queries.md) **隐藏**标志不会关闭冲突，因此这两个标志通常一起设置。

## <a name="hierarchical-overrides"></a>分层覆盖

`HierarchicalStateOverrideComponent`可以附加到对象层次结构的多个级别上。 由于实体上每种类型的组件只能有一个，因此每个`HierarchicalStateOverrideComponent`组件都管理隐藏、浏览、选择、颜色色调和冲突状态。

因此，可以将每个状态设置为：

* `ForceOn`- 此节点上和下方的所有网格都启用状态
* `ForceOff`- 此节点上和下方的所有网线都禁用状态
* `InheritFromParent`- 状态不受此覆盖组件的影响

您可以直接或通过`SetState`函数更改状态：

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>色调

色调覆盖略为特殊，因为同时存在开/关/继承状态和色调属性。 色调的 Alpha 部分定义着色效果的权重：如果设置为 0.0，则没有可见色调，如果设置为 1.0，则对象将以纯色调呈现。 对于中间值，最终颜色将与色调混合。 色调可以按帧更改，以实现颜色动画。

## <a name="performance-considerations"></a>性能注意事项

本身的`HierarchicalStateOverrideComponent`实例不会增加太多的运行时开销。 但是，保持活动组件数量少始终是一种好的做法。 例如，在实现突出显示拾取对象的选择系统时，建议在删除高光时删除组件。 使用中性功能保持组件可以快速累积。

与标准渲染相比，透明渲染在服务器 GPU 上增加工作负载。 如果场景图的大部分切换到 *"透光"，* 许多几何层可见，则可能成为性能瓶颈。 对于具有[选择轮廓](../../overview/features/outlines.md#performance)的对象，也是如此。

## <a name="next-steps"></a>后续步骤

* [概述](../../overview/features/outlines.md)
* [渲染模式](../../concepts/rendering-modes.md)
* [空间查询](../../overview/features/spatial-queries.md)
