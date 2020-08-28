---
title: 材料
description: 渲染材料说明和材料属性
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c9de26d9a83319eee62cf6d30cf2c83f66446fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020246"
---
# <a name="materials"></a>材料

材料是定义如何渲染[网格](meshes.md)的[共享资源](../concepts/lifetime.md)。 材料用于指定要应用的[纹理](textures.md)、是否使对象透明以及如何计算照明。

材料在[模型转换](../how-tos/conversion/model-conversion.md)期间自动创建，可在运行时访问。 还可以通过代码创建自定义材料并替换现有材料。 如果要跨许多网格共享相同资料，此方案特别有意义。 由于对材料的修改在每个引用它的网格上可见，因此此方法可用于轻松应用更改。

> [!NOTE]
> 某些用例（例如突出显示已选取的对象）可以通过修改材料来完成，但通过 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) 实现会轻松得多。

## <a name="material-types"></a>材料类型

Azure 远程渲染具有两种不同的材料类型：

* [PBR 材料](../overview/features/pbr-materials.md)用于应尽可能在物理方面正确渲染的曲面。 使用基于物理方式的渲染 (PBR) 为这些材料计算现实照明。 若要充分利用此材料类型，必须提供高质量输入数据，如粗糙度和法线贴图。

* [颜色材料](../overview/features/color-materials.md)用于不需要额外照明的情况。 这些材料始终全亮，更易于设置。 颜色材料用于根本不应有照明或是已合并静态照明的数据，例如通过 [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry) 获取的模型。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>网格与MeshComponent 材料分配

[网格](meshes.md)具有一个或多个子网格。 每个子网格都引用一个材料。 可以更改材料以在网格上直接使用，也可以在 [MeshComponent](meshes.md#meshcomponent) 上替代要用于子网格的材料。

直接在网格资源上修改材料时，此更改会影响该网格的所有实例。 但在 MeshComponent 上更改它只会影响这一个网格实例。 更合适的方法取决于所需行为，但修改 MeshComponent 是更常见的方法。

## <a name="material-de-duplication"></a>材料重复数据删除

在转换过程中，多个具有相同属性和纹理的材料会自动进行重复数据删除，转变为单个材料。 可以在[转换设置](../how-tos/conversion/configure-model-conversion.md)中禁用此功能，但建议使它保持打开状态以获得最佳性能。

## <a name="material-classes"></a>Material 类

API 提供的所有材料都派生自基类 `Material`。 可以通过 `Material.MaterialSubType` 或直接强制转换来查询其类型：

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>后续步骤

* [PBR 材料](../overview/features/pbr-materials.md)
* [颜色材料](../overview/features/color-materials.md)
