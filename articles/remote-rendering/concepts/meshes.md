---
title: 网 格
description: Azure 远程呈现范围内的模因的定义
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681761"
---
# <a name="meshes"></a>网 格

## <a name="mesh-resource"></a>网格资源

meshes是一个不可变[的共享资源](../concepts/lifetime.md)，只能通过[模型转换](../how-tos/conversion/model-conversion.md)来创建。 meshes 包含一个或多个*子等。* 每个子网格引用默认情况下应呈现材质[的材料](materials.md)。 要将网格放置在 3D 空间中，向[实体](entities.md)添加[网格组件](#meshcomponent)。

### <a name="mesh-resource-properties"></a>网格资源属性

类`Mesh`属性包括：

* **材料：** 一系列材料。 每种材料都由不同的子网格使用。 数组中的多个条目可能引用相同的[材质](materials.md)。 此数据在运行时无法修改。

* **边界：** 网格顶点的局部空间轴对齐边界框 （AABB）。

## <a name="meshcomponent"></a>网格组件

类`MeshComponent`用于放置网格资源的实例。 每个网格组件引用单个网格。 它可以覆盖用于渲染每个子网格的材料。

### <a name="meshcomponent-properties"></a>网格组件属性

* **网格：** 此组件使用的网格资源。

* **材料：** 网格组件本身上指定的材质数组。 数组的长度始终与网格资源上的 *"材质"* 数组相同。 不应从网格默认值中重写的材料将在此数组中设置为*null。*

* **已用材料：** 每个子网格的实际使用材质的数组。 对于非空值，将和 *"材质"* 数组中的数据相同。 否则，它包含网格实例中的 *"材质"* 数组中的值。

## <a name="next-steps"></a>后续步骤

* [材料](materials.md)
