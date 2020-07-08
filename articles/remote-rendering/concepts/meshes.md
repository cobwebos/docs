---
title: 网格
description: Azure 远程呈现范围内网格的定义
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681761"
---
# <a name="meshes"></a>网格

## <a name="mesh-resource"></a>网格资源

网格是不可变的[共享资源](../concepts/lifetime.md)，只能通过[模型转换](../how-tos/conversion/model-conversion.md)来创建。 网格包含一个或多个*submeshes*。 每个子网格都引用一种默认情况下应呈现的[材料](materials.md)。 若要在三维空间中放置网格，请将[MeshComponent](#meshcomponent)添加到[实体](entities.md)。

### <a name="mesh-resource-properties"></a>网格资源属性

`Mesh`类属性为：

* **材料：** 材料的数组。 每个材料由不同的子网格使用。 数组中的多个条目可能引用相同的[材料](materials.md)。 此数据不能在运行时修改。

* **边界：** 网格顶点的本地空间轴对齐的边界框（AABB）。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`类用于放置网格资源的实例。 每个 MeshComponent 都引用单个网格。 它可以重写用于呈现每个子网格的材料。

### <a name="meshcomponent-properties"></a>MeshComponent 属性

* **网格：** 此组件使用的网格资源。

* **材料：** 在网格组件上指定的材料的数组。 该数组的长度始终与网格资源上的*材料*数组的长度相同。 不应从网格默认值覆盖的材料在此数组中设置为*null* 。

* **UsedMaterials：** 每个子网格的实际使用材料的数组。 对于非 null 值，将与*材料*数组中的数据相同。 否则，它包含来自网格实例中的*材料*阵列的值。

## <a name="next-steps"></a>后续步骤

* [材料](materials.md)
