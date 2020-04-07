---
title: 灯光
description: 光源描述和属性
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680942"
---
# <a name="lights"></a>灯光

默认情况下，远程渲染的对象使用[天光](sky.md)点亮。 对于大多数应用程序，这已经足够了，但您可以向场景添加更多光源。

> [!IMPORTANT]
> 只有[PBR 材料](pbr-materials.md)受光源影响。 [颜色材料](color-materials.md)总是显得完全明亮。

> [!NOTE]
> 当前不支持投射阴影。 Azure 远程呈现经过优化，可根据需要使用多个 GPU 来呈现大量几何体。 在这种情形下，传统的阴影投射方法效果不好。

## <a name="common-light-component-properties"></a>常见的光组件属性

所有光类型都派生自抽象基类`LightComponent`并共享这些属性：

* **颜色：**[伽玛空间](https://en.wikipedia.org/wiki/SRGB)中光的颜色。 阿尔法将被忽略。

* **强度：** 光的亮度。 对于点灯和聚光灯，强度也定义光线的照射程度。

## <a name="point-light"></a>点灯

在 Azure 远程`PointLightComponent`渲染中，不仅可以从单个点发出光，还可以从小球体或小管中发出光，以模拟较柔和的光源。

### <a name="pointlightcomponent-properties"></a>点光组件属性

* **半径：** 默认半径为零，在这种情况下，灯光充当点光。 如果半径大于零，则充当球形光源，从而更改镜面高光的外观。

* **长度：** 如果两`Length`者`Radius`均为非零，则光充当管灯。 这可用于模拟霓虹灯管。

* **衰减截止：** 如果留给 （0，0） 则光的衰减仅取决于其`Intensity`。 但是，您可以提供自定义的最小/最大距离，光的强度会线性缩小到 0。 此功能可用于强制特定光的影响范围较小。

* **投影立方体图：** 如果设置为有效的[立方体贴图](../../concepts/textures.md)，则纹理将投影到灯光的周围几何体上。 立方体贴图的颜色使用灯光的颜色进行调制。

## <a name="spot-light"></a>聚光灯

`SpotLightComponent`与 类似，`PointLightComponent`但光线被约束到圆锥体的形状。 圆锥的方向由*所有者实体的负 z 轴*定义。

### <a name="spotlightcomponent-properties"></a>聚光灯组件属性

* **半径：** 与 相同`PointLightComponent`。

* **点角：** 此间隔定义锥体的内角和外角，以度为单位进行测量。 内角内的所有内容都以全亮度亮起。 衰落应用于产生五边形效果的外角。

* **衰降指数：** 定义内锥角和外锥角之间的衰落过渡的剧烈程度。 值越高，转换越清晰。 默认值 1.0 会导致线性转换。

* **衰减截止：** 与 相同`PointLightComponent`。

* **投影2d纹理：** 如果设置为有效的[2D 纹理](../../concepts/textures.md)，则图像将投影到光线照射的几何体上。 纹理的颜色使用灯光的颜色进行调制。

## <a name="directional-light"></a>定向光

模拟`DirectionalLightComponent`无限远的光源。 光线照射到*所有者实体的负 z 轴*的方向。 实体的位置将被忽略。

没有其他属性。

## <a name="performance-considerations"></a>性能注意事项

光源对渲染性能有显著影响。 仅在应用程序需要时才谨慎使用它们。 任何静态全局照明条件（包括静态方向组件）都可以通过[自定义天空纹理](sky.md)来实现，无需额外的渲染成本。

## <a name="next-steps"></a>后续步骤

* [材料](../../concepts/materials.md)
* [天空](sky.md)
