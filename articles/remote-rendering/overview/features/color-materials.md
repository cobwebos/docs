---
title: 颜色材料
description: 描述颜色材料类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681072"
---
# <a name="color-materials"></a>颜色材料

*颜色材料*是 Azure 远程呈现中支持的[材料类型](../../concepts/materials.md)之一。 它们用于不应接收任何类型照明的[网格](../../concepts/meshes.md)，而是始终使用完全亮度。 这种情况可能适用于 "光亮" 材料，如汽车仪表板、轻型电灯泡或已合并静态照明的数据（例如通过[photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)获取的模型）。

颜色材料比[.pbr 材料](pbr-materials.md)更高效，因为它的深浅底纹模型更为简单。 它们还支持不同的透明模式。

## <a name="common-material-properties"></a>常用材料属性

这些属性是所有材料共有的：

* **albedoColor：** 此颜色与其他颜色（如*albedoMap*或*顶点颜色*）相乘。 如果对材料启用了*透明度*，则使用 alpha 通道调整不透明度， `1`这意味着完全不透明并`0`表示完全透明。 默认值为白色。

  > [!NOTE]
  > 由于颜色材料不反映环境，因此完全透明的颜色材料将变得不可见。 这对于[.pbr 材料](pbr-materials.md)是不同的。

* **albedoMap：** 每像素 albedo 值的[2d 纹理](../../concepts/textures.md)。

* **alphaClipEnabled**和**AlphaClipThreshold：** 如果*alphaClipEnabled*为 true，则不会绘制 albedo alpha 值小于*alphaClipThreshold*的所有像素。 即使不启用透明度，也可以使用 Alpha 剪辑，因而速度要快得多。 不过，Alpha 剪裁材料的呈现速度仍比完全不透明材料慢。 默认情况下，alpha 剪辑处于禁用状态。

* **textureCoordinateScale**和**textureCoordinateOffset：** 将刻度与 UV 纹理坐标相乘，并向其添加偏移量。 可用于拉伸和移动纹理。 默认小数位数为（1，1），偏移量为（0，0）。

* **useVertexColor：** 如果网格包含顶点颜色，并且已启用此选项，则网格的顶点颜色将被相乘到*albedoColor*和*albedoMap*中。 默认情况下，顶点颜色处于禁用状态。

* **isDoubleSided：** 如果将 sidedness 设置为 true，则即使相机正在查看背景面，也会呈现带有此材料的三角形。 默认情况下，此选项处于禁用状态。 另请参阅[单面渲染](single-sided-rendering.md)。

## <a name="color-material-properties"></a>颜色材料属性

以下属性特定于颜色材料：

* **vertexMix：** 与`0` `1`之间的此值用于指定[网格](../../concepts/meshes.md)中的顶点颜色对最终颜色的影响程度。 默认值为1时，顶点颜色完全与 albedo 颜色相乘。 如果值为0，则完全忽略顶点颜色。

* **transparencyMode：** 与[.pbr 材料](pbr-materials.md)相反，颜色材料区分不同的透明模式：

  1. 不**透明：** 默认模式禁用透明度。 不过，即使有足够的，也仍然可以进行 Alpha 剪裁。
  
  1. **AlphaBlended：** 此模式类似于 .PBR 材料的透明模式。 它应该用于查看-通过玻璃等材料。

  1. **累加性：** 此模式是最简单且最有效的透明模式。 将材料的贡献添加到呈现的图像。 此模式可用于模拟光亮（但仍是透明）对象，例如用于突出显示重要对象的标记。

## <a name="next-steps"></a>后续步骤

* [PBR 材料](pbr-materials.md)
* [纹理](../../concepts/textures.md)
* [网格](../../concepts/meshes.md)
