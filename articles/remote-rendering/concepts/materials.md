---
title: 材料
description: 渲染材质描述和材质属性
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681839"
---
# <a name="materials"></a>材料

材质是定义如何呈现[等同](meshes.md)的[共享资源](../concepts/lifetime.md)。 材质用于指定要应用的[纹理](textures.md)、是否使对象透明以及如何计算照明。

材料在[模型转换](../how-tos/conversion/model-conversion.md)期间自动创建，并在运行时访问。 您还可以从代码创建自定义材料并替换现有材料。 如果要跨多个网点共享相同的材料，此方案尤其有意义。 由于材质的修改在引用材质的每个网格上都可见，因此此方法可用于轻松应用更改。

> [!NOTE]
> 某些用例（如突出显示选取的对象）可以通过修改材质来完成，但通过[分层状态覆盖组件](../overview/features/override-hierarchical-state.md)更容易实现。

## <a name="material-types"></a>材料类型

Azure 远程呈现有两种不同的材质类型：

* [PBR 材质](../overview/features/pbr-materials.md)用于应尽可能呈现为物理正确曲面。 使用*基于物理的渲染*（PBR） 计算这些材料的真实照明。 为了充分利用这种材料类型，提供高质量的输入数据（如粗糙度和法线贴图）非常重要。

* [颜色材料](../overview/features/color-materials.md)用于不需要额外照明的情况。 这些材料总是充满明亮，更容易设置。 颜色材料用于本应没有照明或已包含静态照明的数据，例如通过[摄影测量](https://en.wikipedia.org/wiki/Photogrammetry)获得的模型。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>网格与网格组件材料分配

[梅斯](meshes.md)有一个或多个子百年代。 每个子网格引用一个材质。 您可以更改材质以直接在网格上使用，也可以重写要用于[网格组件](meshes.md#meshcomponent)上的子网格的材料。

直接在网格资源上修改材质时，此更改会影响该网格的所有实例。 但是，在网格组件上更改它仅影响该网格实例。 哪种方法更合适取决于所需的行为，但修改 Mesh组件是更常见的方法。

## <a name="material-classes"></a>材料类

API 提供的所有材料都派生自基类`Material`。 可通过`Material.MaterialSubType`或直接强制转换它们来查询其类型：

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
