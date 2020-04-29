---
title: 天空反射
description: 描述如何为天空反射设置环境映射
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680604"
---
# <a name="sky-reflections"></a>天空反射

在 Azure 远程呈现中，天空纹理用于对对象进行实际光。 对于增加的现实应用程序，这种纹理应类似于你的真实环境，使对象显得更有说服力。 本文介绍如何更改天空纹理。

> [!NOTE]
> 天空纹理也称为*环境映射*。 这些术语可以互换使用。

## <a name="object-lighting"></a>对象照明

Azure 远程呈现使用*基于物理的呈现*（.pbr）来实现实际的照明计算。 虽然您可以将[光源](lights.md)添加到场景，但使用良好的天空会影响最大。

下面的图像显示了仅使用天空纹理的照明不同曲面的结果：

| 程度  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 非金属  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

有关照明模型的详细信息，请参阅[材料](../../concepts/materials.md)章节。

> [!IMPORTANT]
> Azure 远程呈现仅对照明模型使用天空纹理。 它不会将天空呈现为背景，因为扩充的现实应用程序已经具有适当的背景（真实世界）。

## <a name="changing-the-sky-texture"></a>更改天空纹理

若要更改环境映射，只需[加载纹理](../../concepts/textures.md)并更改会话的`SkyReflectionSettings`：

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

请注意， `LoadTextureFromSASAsync`由于加载了内置纹理，因此上面使用了该变量。 如果纹理是从[关联的 Blob 存储](../../how-tos/create-an-account.md#link-storage-accounts)加载的，请使用 `LoadTextureAsync` 变体。

## <a name="sky-texture-types"></a>天空纹理类型

可以同时使用*[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* 和*2d 纹理*作为环境地图。

所有纹理都必须采用支持的[纹理格式](../../concepts/textures.md#supported-texture-formats)。 不需要为天空纹理提供 mipmap。

### <a name="cube-environment-maps"></a>多维数据集环境映射

下面是一个已解包的立方体贴图：

![已解包的立方体贴图](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` / 使用`LoadTextureFromSASAsync` with `TextureType.CubeMap`来加载立方体贴图纹理。

### <a name="sphere-environment-maps"></a>球环境地图

将二维纹理用作环境图时，图像必须位于[球状坐标空间](https://en.wikipedia.org/wiki/Spherical_coordinate_system)中。

![球形坐标中的天空图像](media/spheremap-example.png)

与`AzureSession.Actions.LoadTextureAsync`结合`TextureType.Texture2D`使用来加载球面环境地图。

## <a name="built-in-environment-maps"></a>内置环境映射

Azure 远程呈现提供一些始终可用的内置环境地图。 所有内置环境映射都是 cubemaps 的。

|标识符                         | 说明                                              | 图示                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 各种条带灯，明亮室内基本照明    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | 强光灯具设置，多窗口灯      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | 中型灯具设置中的 Varyingly 彩色灯光  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | 适度明亮的环境厅                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 具有浅色深色对比度的立体室内设置              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 日间环境房间细，明亮的窗口区域     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | 较深的夜间   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | 鲜直射和阴影对比度                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 用中等地面照明清除天空            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 适度变化的 sun 和底纹                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 晚上日落的浅里                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 亮、lush、绿和淡音 | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 具有明亮环境地面的白天                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | 与 TearsOfSteelBridge 相同                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>后续步骤

* [光](../../overview/features/lights.md)
* [材料](../../concepts/materials.md)
* [纹理](../../concepts/textures.md)
* [TexConv 命令行工具](../../resources/tools/tex-conv.md)
