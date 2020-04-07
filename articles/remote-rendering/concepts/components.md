---
title: 组件
description: Azure 远程呈现范围内组件的定义
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681891"
---
# <a name="components"></a>组件

Azure 远程呈现使用[实体组件系统](https://en.wikipedia.org/wiki/Entity_component_system)模式。 虽然[实体](entities.md)表示对象的位置和层次结构，但组件负责实现行为。

最常用的组件类型是[网格组件](meshes.md)，它们向呈现管道中添加网格。 同样，[光组件](../overview/features/lights.md)用于添加照明，[切割平面组件](../overview/features/cut-planes.md)用于剪切打开的构件。

所有这些组件都使用它们所附加到的实体的变换（位置、旋转、比例）作为参考点。

## <a name="working-with-components"></a>使用组件

您可以轻松地以编程方式添加、删除和操作组件：

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

组件在创建时附加到实体。 之后无法将其移动到其他实体。 当组件的所有者实体被销毁时`Component.Destroy()`，将显式删除或自动删除组件。

一次只能将每个组件类型的一个实例添加到实体中。

## <a name="unity-specific"></a>特定于统一

Unity 集成具有用于与组件交互的其他扩展功能。 请参阅[Unity 游戏对象和组件](../how-tos/unity/objects-components.md)。

## <a name="next-steps"></a>后续步骤

* [对象边界](object-bounds.md)
* [网 格](meshes.md)
