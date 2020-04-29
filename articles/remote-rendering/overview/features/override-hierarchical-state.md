---
title: 分层状态替代
description: 说明分层状态覆盖组件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680786"
---
# <a name="hierarchical-state-override"></a>分层状态替代

在许多情况下，需要动态更改[模型](../../concepts/models.md)各部分的外观，例如隐藏 subgraphs 或将部件切换为透明渲染。 更改所涉及的每个部分的材料并不实用，因为它需要遍历整个场景图，并管理每个节点上的材料克隆和分配。

若要通过尽可能少的开销完成此用例，请`HierarchicalStateOverrideComponent`使用。 此组件实现场景图的任意分支上的分层状态更新。 这意味着，可以在场景图中的任何级别上定义一个状态，并且它会在层次结构中 trickles，直到它被新状态覆盖或应用于叶对象。

作为示例，请考虑汽车的模型，并且想要将整个汽车转换为透明的，除了内部引擎部件。 此用例仅涉及组件的两个实例：

* 第一个组件分配给模型的根节点，并为整个汽车启用透明渲染。
* 第二个组件分配给引擎的根节点，并通过显式关闭 "查看" 模式再次重写该状态。

## <a name="features"></a>功能

可以重写的一组固定状态为：

* **隐藏**：场景图中的相应网格将隐藏或显示。
* **淡色颜色**：呈现的对象可以使用其各自的淡色颜色和色调粗细进行彩色着色。 下图显示了色轮边缘的颜色颜色。
  
  ![颜色色调](./media/color-tint.png)

* **请参见-通过**：几何图形以半透明方式呈现，例如，用于显示对象的内部部件。 下图显示了在查看模式下呈现的整个汽车（red 刹车 caliper 除外）：

  ![请参阅](./media/see-through.png)

  > [!IMPORTANT]
  > 仅在使用*TileBasedComposition* [呈现模式](../../concepts/rendering-modes.md)时，才能使用 "仅查看" 效果。

* **Selected**：使用[选择轮廓](outlines.md)呈现几何。

  ![选择轮廓](./media/selection-outline.png)

* **DisableCollision**：几何图形从[空间查询](spatial-queries.md)中免除。 **隐藏**标志不会关闭冲突，因此，这两个标志通常一起设置。

## <a name="hierarchical-overrides"></a>分层覆盖

`HierarchicalStateOverrideComponent`可以附加在对象层次结构的多个级别上。 由于实体上只有每个类型的一个组件，因此每个`HierarchicalStateOverrideComponent`组件都管理隐藏的状态、查看、选定、颜色淡色和冲突。

因此每个状态都可以设置为以下其中之一：

* `ForceOn`-为此节点上和下面的所有网格启用状态
* `ForceOff`-对于此节点上和此节点下的所有网格，禁用状态
* `InheritFromParent`-状态不受此重写组件影响

可以直接或通过`SetState`函数更改状态：

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>淡色颜色

淡色颜色重写略有特殊，因为两者都具有 "开/关"/"已禁用/继承" 状态和 "淡色颜色" 属性。 色调颜色的 alpha 部分定义色调效果的权重：如果设置为0.0，则不显示淡色颜色; 如果设置为1.0，则将以纯色调颜色呈现对象。 对于 in 值，最终颜色将与淡色颜色混合。 可以更改每帧的色调颜色，以实现颜色动画。

## <a name="performance-considerations"></a>性能注意事项

实例`HierarchicalStateOverrideComponent`本身不会增加更多的运行时开销。 但是，保持活动组件数量的降低始终是一种很好的做法。 例如，在实现突出显示所选对象的选择系统时，建议在删除突出显示时删除该组件。 使组件保持在非特定功能的周围会迅速增加。

透明渲染比标准渲染在服务器 Gpu 上增加了更多工作负荷。 如果场景图的大型部分已切换到 "*查看*"，并且有许多几何可见，则可能会成为性能瓶颈。 这对于具有[选择轮廓](../../overview/features/outlines.md#performance)的对象是有效的。

## <a name="next-steps"></a>后续步骤

* [大纲](../../overview/features/outlines.md)
* [渲染模式](../../concepts/rendering-modes.md)
* [空间查询](../../overview/features/spatial-queries.md)
