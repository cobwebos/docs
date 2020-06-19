---
title: 分层状态替代
description: 解释分层状态替代组件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 40857e83457222365e61a224ead19bd1d1d31ae7
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758973"
---
# <a name="hierarchical-state-override"></a>分层状态替代

在许多情况下，需要动态更改[模型](../../concepts/models.md)部分的外观，例如隐藏子图或将部分切换为透明渲染。 更改所涉及的每个部分的材料是不切实际的，因为它需要遍历整个场景图，并管理每个节点上的材料克隆和分配。

若要以尽可能少的开销完成此用例，请使用 `HierarchicalStateOverrideComponent`。 该组件在场景图的任意分支上实现分层状态更新。 这意味着，可以在场景图中的任何级别上定义状态，并且它会在层次结构中传递，直到它被新状态覆盖或应用于叶对象。

举例说明，试想汽车的模型，你希望将整个汽车切换为透明的，除了内部引擎部分。 此用例仅涉及组件的两个实例：

* 第一个组件分配给模型的根节点，并为整个汽车启用透明渲染。
* 第二个组件分配给引擎的根节点，并通过显式关闭透视模式再次替代该状态。

## <a name="features"></a>功能

可以替代的一组固定状态为：

* **Hidden**：场景图中的相应网格将隐藏或显示。
* **Tint color**：渲染的对象可以使用其各自的淡化颜色和淡化权重进行颜色淡化。 下图显示了对轮缘进行颜色淡化。
  
  ![颜色淡化](./media/color-tint.png)

* **See-through**：几何结构以半透明方式呈现，例如，显示对象的内部部件。 下图显示了整个汽车以透视模式呈现，红色制动钳除外：

  ![See-Through](./media/see-through.png)

  > [!IMPORTANT]
  > 仅当使用“TileBasedComposition”[渲染模式](../../concepts/rendering-modes.md)时，透视效果才起作用。

* **Selected**：几何结构呈现有[选择轮廓](outlines.md)。

  ![选择轮廓](./media/selection-outline.png)

* **DisableCollision**：几何结构将不受[空间查询](spatial-queries.md)的影响。 **Hidden** 标志不会关闭冲突，因此，这两个标志通常一起设置。

## <a name="hierarchical-overrides"></a>分层替代

`HierarchicalStateOverrideComponent` 可以附加在对象层次结构的多个级别上。 由于一个实体上只能有每个类型的一个组件，因此每个 `HierarchicalStateOverrideComponent` 管理隐藏、透视、选定、颜色淡化和冲突等状态。

因此每个状态都可以设置为以下之一：

* `ForceOn` - 该状态会为此节点之上和之下的所有网格启用
* `ForceOff` - 该状态会为此节点之上和之下的所有网格禁用
* `InheritFromParent` - 该状态不受此替代组件影响

可以直接更改状态，也可以通过 `SetState` 函数更改状态：

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->HiddenState(HierarchicalEnableState::ForceOn);

// set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>浅色

浅色替代略有特殊，因为它既有开/关/继承状态，又有浅色属性。 浅色的 Alpha 部分定义淡化效果的权重：如果设置为 0.0，则不会显示浅色，如果设置为 1.0，则将以纯浅色呈现对象。 对于中间值，最终颜色将与浅色混合。 可以在每帧的基础上更改浅色，以实现彩色动画。

## <a name="performance-considerations"></a>性能注意事项

`HierarchicalStateOverrideComponent` 本身的实例不会增加很多运行时开销。 但是，保持较低的活动组件数量始终是一个好习惯。 例如，在实现突出显示所选对象的选择系统时，建议在删除突出显示时删除该组件。 无相关功能的组件会迅速累积。

透明渲染比标准渲染在服务器 GPU 上增加了更多工作负载。 如果场景图的大型部分切换到 *see-through*，且几何结构多个层次可见，则可能会成为性能瓶颈。 这对于具有[选择轮廓](../../overview/features/outlines.md#performance)的对象而言也是如何。

## <a name="next-steps"></a>后续步骤

* [轮廓](../../overview/features/outlines.md)
* [渲染模式](../../concepts/rendering-modes.md)
* [空间查询](../../overview/features/spatial-queries.md)
