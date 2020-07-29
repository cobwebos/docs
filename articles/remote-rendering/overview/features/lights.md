---
title: 场景照明
description: 光源说明和属性
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: e33e012480c876dc5befbb93404bdb131ea9329a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022140"
---
# <a name="scene-lighting"></a>场景照明

默认情况下，远程呈现的对象将使用[天空](sky.md)亮起。 对于大多数应用程序，这已经足够，但您可以将更多的光源添加到场景中。

> [!IMPORTANT]
> 只有[.pbr 材料](pbr-materials.md)受光源的影响。 [颜色材料](color-materials.md)始终显示为非常明亮。

> [!NOTE]
> 当前不支持强制转换阴影。 Azure 远程呈现经过优化，可呈现大量几何，如有必要，利用多个 Gpu。 在这种情况下，用于影子转换的传统方法并不能正常工作。

## <a name="common-light-component-properties"></a>常见轻型组件属性

所有光源类型都派生自抽象基类 `LightComponent` 并共享这些属性：

* **颜色：**[伽玛空间](https://en.wikipedia.org/wiki/SRGB)中的光的颜色。 忽略 Alpha。

* **亮度：** 光的亮度。 对于点光和投射光，强度还可定义光线的光线。

## <a name="point-light"></a>点光

在 Azure 远程呈现中，不仅 `PointLightComponent` 可以从单一点发出光，还可以从小球或小型电子管发出光，以模拟较轻的光源。

### <a name="pointlightcomponent-properties"></a>PointLightComponent 属性

* **Radius：** 默认半径为零，在这种情况下，光用作点光。 如果半径大于零，则它将充当球面光源，这将更改反射高光的外观。

* **长度：** 如果 `Length` 和 `Radius` 都非零，则光源将作为电子管光。 这可用于模拟霓虹灯电子管。

* **AttenuationCutoff：** 如果左到（0，0），则光的衰减仅依赖于其 `Intensity` 。 不过，你可以提供自定义的最小/最大距离，将光线强度缩小到0。 此功能可用于对特定光源强制执行较小的影响范围。

* **ProjectedCubemap：** 如果设置为有效的[立方体贴图](../../concepts/textures.md)，纹理会投影到光源的周围几何。 立方体贴图的颜色是用光的颜色进行调制。

## <a name="spot-light"></a>污点

与 `SpotLightComponent` 类似， `PointLightComponent` 但光源被约束为圆锥的形状。 圆锥的方向由*所有者实体的负 z 轴*定义。

### <a name="spotlightcomponent-properties"></a>SpotLightComponent 属性

* **Radius：** 与相同 `PointLightComponent` 。

* **SpotAngleDeg：** 此间隔定义圆锥的内部和外部角度，以度为单位。 内径内的所有内容都以完全亮度来亮起。 衰减适用于产生类似于 penumbra 效果的外部角度。

* **FalloffExponent：** 定义在内部和外部锥角之间过渡的急剧度。 值越大，转换越清晰。 默认值为1.0 将导致线性转换。

* **AttenuationCutoff：** 与相同 `PointLightComponent` 。

* **Projected2dTexture：** 如果设置为有效的[2d 纹理](../../concepts/textures.md)，则会将图像投影到光线在其上工作的几何。 纹理的颜色与光源颜色一起进行了调制。

## <a name="directional-light"></a>定向光

`DirectionalLightComponent`模拟无限远的光源。 光将进入*所有者实体的负 z 轴*方向。 忽略实体的位置。

没有其他属性。

## <a name="performance-considerations"></a>性能注意事项

光源对渲染性能有重大影响。 请谨慎使用，仅在应用程序需要时使用。 任何静态全局照明条件（包括静态方向组件）都可以通过[自定义天空纹理](sky.md)来实现，无需额外的呈现开销。

## <a name="next-steps"></a>后续步骤

* [材料](../../concepts/materials.md)
* [天空](sky.md)
