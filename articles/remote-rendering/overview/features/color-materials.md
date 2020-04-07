---
title: 颜色材料
description: 描述颜色材质类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681072"
---
# <a name="color-materials"></a>颜色材料

*颜色材质*是 Azure 远程渲染中支持[的材料类型](../../concepts/materials.md)之一。 它们用于不应接收任何照明的[网，](../../concepts/meshes.md)而是随时充满亮度。 对于"发光"材料（如汽车仪表板、灯泡）或已经集成了静态照明的数据（例如通过[摄影测量](https://en.wikipedia.org/wiki/Photogrammetry)获得的模型）来说，情况可能如此。

颜色材质的渲染效率比[PBR 材质](pbr-materials.md)更有效，因为它们的着色模型更简单。 它们还支持不同的透明度模式。

## <a name="common-material-properties"></a>常见材料属性

这些属性是所有材料的通用属性：

* **阿贝多颜色：** 此颜色与其他颜色（如*反色映射*或*顶点颜色*）相乘。 如果在材质上启用*了透明度*，则 Alpha 通道用于调整不透明度，`1`其含义完全不透明，`0`含义完全透明。 默认值为白色。

  > [!NOTE]
  > 由于颜色材料不反映环境，因此完全透明的彩色材质变得不可见。 对于[PBR 材料](pbr-materials.md)，这是不同的。

* **阿尔贝多地图：** 每像素反贝度值的[2D 纹理](../../concepts/textures.md)。

* **AlphaClip 启用**和**alphaClip 阈值：** 如果*AlphaClip 启用*为 true，则不会绘制所有白数 alpha 值低于*alphaClip 阈值*的像素。 即使不启用透明度，也可以使用 Alpha 裁剪，渲染速度也快得多。 不过，Alpha 剪切材质渲染的速度仍然比完全不透明的材料慢。 默认情况下，禁用 alpha 剪辑。

* **纹理坐标和****纹理坐标偏移：** 比例乘以 UV 纹理坐标，偏移量添加到其中。 可用于拉伸和移动纹理。 默认比例为 （1，1），偏移量为 （0， 0）。

* **使用VertexColor：** 如果网格包含顶点颜色并启用此选项，则网格的顶点颜色将乘以*反色*和*反反多映射*。 默认情况下，顶点颜色被禁用。

* **是双面：** 如果双面设置为 true，则即使摄像机正在查看其背面，也会渲染具有此材质的三角形。 默认情况下，此选项被禁用。 另请参阅[单面渲染](single-sided-rendering.md)。

## <a name="color-material-properties"></a>颜色材质属性

以下属性特定于颜色材质：

* **顶点混合：** 值之间的`0`此值`1`指定[网格](../../concepts/meshes.md)中的顶点颜色对最终颜色的贡献程度。 在默认值 1 时，顶点颜色将完全乘以反搜索度颜色。 值为 0 时，将完全忽略顶点颜色。

* **透明度模式：** 与[PBR 材料](pbr-materials.md)相反，颜色材料区分不同的透明度模式：

  1. **不透明：** 默认模式禁用透明度。 但是，Alpha 剪辑仍然是可能的，如果足够，应该优先。
  
  1. **阿尔法混合：** 此模式类似于 PBR 材料的透明度模式。 它应该用于玻璃等透镜材料。

  1. **添加剂：** 此模式是最简单、最有效的透明度模式。 材质的贡献将添加到渲染的图像中。 此模式可用于模拟发光（但仍透明）对象，例如用于突出显示重要对象的标记。

## <a name="next-steps"></a>后续步骤

* [PBR 材料](pbr-materials.md)
* [纹理](../../concepts/textures.md)
* [网 格](../../concepts/meshes.md)
