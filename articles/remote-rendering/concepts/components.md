---
title: 组件
description: Azure 远程呈现范围内组件的定义
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681891"
---
# <a name="components"></a>组件

Azure 远程呈现使用[实体组件系统](https://en.wikipedia.org/wiki/Entity_component_system)模式。 虽然[实体](entities.md)表示对象的位置和层次结构组合，但组件负责实现行为。

最常用的组件类型是[网格组件](meshes.md)，这些组件将网格添加到呈现管道中。 同样，使用[光源组件](../overview/features/lights.md)来添加光源和[切口平面组件](../overview/features/cut-planes.md)来剪切打开的网格。

所有这些组件都使用它们附加到的实体的转换（位置、旋转、缩放）作为其引用点。

## <a name="working-with-components"></a>使用组件

可以通过编程方式轻松地添加、删除和操作组件：

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

组件在创建时附加到实体。 以后不能将其移动到其他实体。 当组件的所有者实体`Component.Destroy()`销毁时，组件将以显式方式删除或自动删除。

一次只能向一个实体中添加每个组件类型的一个实例。

## <a name="unity-specific"></a>Unity 特定

Unity 集成具有用于与组件进行交互的其他扩展函数。 请参阅[Unity 游戏对象和组件](../how-tos/unity/objects-components.md)。

## <a name="next-steps"></a>后续步骤

* [对象边界](object-bounds.md)
* [网格](meshes.md)
