---
title: 材料
description: 呈现材料描述和材料属性
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681839"
---
# <a name="materials"></a>材料

材料是用于定义[网格](meshes.md)呈现方式的[共享资源](../concepts/lifetime.md)。 材料用于指定要应用的[纹理](textures.md)、是否使对象透明以及如何计算光照。

材料是在[模型转换](../how-tos/conversion/model-conversion.md)期间自动创建的，并且可在运行时进行访问。 还可以通过代码创建自定义材料并替换现有材料。 如果您想要跨多个网格共享同一资料，此方案特别有用。 由于对材料的修改在每个引用它的网格上可见，因此此方法可用于轻松应用更改。

> [!NOTE]
> 某些用例（例如突出显示的对象）可以通过修改材料来完成，但通过[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)实现起来要容易得多。

## <a name="material-types"></a>材料类型

Azure 远程呈现具有两种不同的材料类型：

* 对于应尽可能以物理方式正确呈现的图面，将使用[.pbr 材料](../overview/features/pbr-materials.md)。 使用*基于物理的呈现*（.pbr）为这些材料计算现实照明。 若要充分利用这种材料类型，必须提供高质量的输入数据，如粗糙度和普通地图。

* [颜色材料](../overview/features/color-materials.md)用于不需要其他照明的情况。 这些材料始终具有完整的亮点，更易于设置。 颜色材料用于应根本不使用光照或已合并静态照明的数据，例如通过[photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)获得的模型。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>网格与 MeshComponent 材料分配

[网格](meshes.md)有一个或多个 submeshes。 每个子网格都引用一个材料。 您可以更改要在网格上直接使用的材料，也可以在[MeshComponent](meshes.md#meshcomponent)上覆盖要用于子网格的材料。

直接在网格资源上修改材料时，此更改会影响该网格的所有实例。 但在 MeshComponent 上更改它只会影响一个网格实例。 更合适的方法取决于所需的行为，但修改 MeshComponent 是更常见的方法。

## <a name="material-classes"></a>材料类

API 提供的所有资料派生自基类`Material`。 可以通过`Material.MaterialSubType`或直接强制转换其类型来查询其类型：

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>后续步骤

* [PBR 材料](../overview/features/pbr-materials.md)
* [颜色材料](../overview/features/color-materials.md)
