---
title: 天空反射
description: 描述如何为天空反射设置环境地图
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680604"
---
# <a name="sky-reflections"></a>天空反射

在 Azure 远程渲染中，天空纹理用于真实地照亮对象。 对于增强现实应用，此纹理应类似于实际环境，以使对象看起来令人信服。 本文介绍如何更改天空纹理。

> [!NOTE]
> 天空纹理也称为*环境地图*。 这些术语可互换使用。

## <a name="object-lighting"></a>对象照明

Azure 远程渲染采用*基于物理的渲染*（PBR） 进行逼真的照明计算。 尽管您可以将[光源](lights.md)添加到场景中，但使用良好的天空纹理影响最大。

下图仅显示仅使用天空纹理照亮不同曲面的结果：

| 粗糙 度  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 非金属  | ![介电0](media/dielectric-0.png)   | ![绿点公园](media/dielectric-0.25.png)  | ![绿点公园](media/dielectric-0.5.png)  | ![绿点公园](media/dielectric-0.75.png)  | ![绿点公园](media/dielectric-1.png)  |
| Metal      | ![绿点公园](media/metallic-0.png)  | ![绿点公园](media/metallic-0.25.png)    | ![绿点公园](media/metallic-0.5.png)    | ![绿点公园](media/metallic-0.75.png)    | ![绿点公园](media/metallic-1.png)    |

有关照明模型的详细信息，请参阅[材料](../../concepts/materials.md)章节。

> [!IMPORTANT]
> Azure 远程渲染仅对照明模型使用天空纹理。 它不渲染天空作为背景，因为增强现实应用程序已经有一个适当的背景 - 现实世界。

## <a name="changing-the-sky-texture"></a>更改天空纹理

要更改环境映射，只需[加载纹理](../../concepts/textures.md)并更改会话的`SkyReflectionSettings`：

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

请注意，`LoadTextureFromSASAsync`该变体用于上述，因为已加载内置纹理。 如果从[链接的 Blob 存储](../../how-tos/create-an-account.md#link-storage-accounts)加载，请使用变体`LoadTextureAsync`。

## <a name="sky-texture-types"></a>天空纹理类型

您可以将*[立方体贴图](https://en.wikipedia.org/wiki/Cube_mapping)* 和*2D 纹理*用作环境贴图。

所有纹理必须采用[支持的纹理格式](../../concepts/textures.md#supported-texture-formats)。 您无需为天空纹理提供 mipmap。

### <a name="cube-environment-maps"></a>立方体环境地图

供参考，下面是一个未包装的立方体图：

![未包装的立方体图](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync`/ 用于`LoadTextureFromSASAsync`加载`TextureType.CubeMap`立方体贴图纹理。

### <a name="sphere-environment-maps"></a>球体环境图

当使用 2D 纹理作为环境贴图时，图像必须位于[球形坐标空间](https://en.wikipedia.org/wiki/Spherical_coordinate_system)中。

![球形坐标中的天空图像](media/spheremap-example.png)

用于`AzureSession.Actions.LoadTextureAsync``TextureType.Texture2D`加载球形环境贴图。

## <a name="built-in-environment-maps"></a>内置环境图

Azure 远程呈现提供了一些始终可用的内置环境映射。 所有内置环境地图都是立方体贴图。

|标识符                         | 说明                                              | 图示                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 各种条纹灯，明亮的室内基础照明    | ![自动商店](media/autoshop.png)
|builtin://BoilerRoom               | 明亮的室内灯光设置，多个窗灯      | ![锅炉房](media/boiler-room.png)
|builtin://ColorfulStudio           | 中等灯光室内设置中颜色不同的灯光  | ![彩色工作室](media/colorful-studio.png)
|builtin://Hangar                   | 中等明亮的环境大厅灯                     | ![小汉加尔](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 暗暗室内设置，形成浅暗对比度              | ![工业管道和阀门](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 白天环境室灯，明亮的窗户区域光线     | ![莱邦博](media/lebombo.png)
|builtin://SataraNight              | 黑暗的夜空和地面，有许多周围的灯光   | ![萨塔拉之夜](media/satara-night.png)
|builtin://SunnyVondelpark          | 明亮的阳光和阴影对比度                      | ![桑尼冯德尔帕克](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 清晰的天光，具有适度的地面照明            | ![西费尔方丹](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 适度变化的太阳和阴影                         | ![钢桥的眼泪](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 傍晚日落灯接近黄昏                    | ![威尼斯日落](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明亮、郁郁葱葱的绿色和白色光色调，昏暗的地面 | ![惠普勒克里克区域公园](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 白天，具有明亮的环境地面光                 | ![温特河](media/winter-river.png)
|builtin://DefaultSky               | 与《钢铁桥的眼泪》一样                               | ![默认天空](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>后续步骤

* [灯](../../overview/features/lights.md)
* [材料](../../concepts/materials.md)
* [纹理](../../concepts/textures.md)
* [TexConv 命令行工具](../../resources/tools/tex-conv.md)
