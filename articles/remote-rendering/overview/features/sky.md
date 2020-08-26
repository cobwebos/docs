---
title: 天空反射
description: 介绍了如何为天空反射设置环境地图
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: be3dc2b113cb21c2dfb54a29e7f426e0d925c6d9
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759109"
---
# <a name="sky-reflections"></a>天空反射

在 Azure 远程渲染中，天空纹理用于真实地照亮物体。 在增强现实应用程序中，此纹理应类似于真实的周边环境，使物体显得更生动。 本文介绍了如何更改天空纹理。

> [!NOTE]
> 天空纹理也称为环境地图。 这两个术语可以互换使用。

## <a name="object-lighting"></a>物体照明

Azure 远程渲染运用基于物理学的渲染 (PBR) 来计算现实照明。 尽管你可以向场景中添加[光源](lights.md)，但使用合适的天空纹理可以产生最棒的效果。

下面的图像显示了仅使用天空纹理来照亮各种表面的效果：

| 粗糙度  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 非金属  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| 金属      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

有关照明模型的详细信息，请参阅[材料](../../concepts/materials.md)一章。

> [!IMPORTANT]
> Azure 远程渲染仅将天空纹理用于照明模型。 它不会将天空渲染为背景，因为增强现实应用程序已有适当的背景 - 真实世界。

## <a name="changing-the-sky-texture"></a>更改天空纹理

若要更改环境地图，只需[加载纹理](../../concepts/textures.md)并更改会话的 `SkyReflectionSettings`：

```cs
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

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            ApiHandle<SkyReflectionSettings> settings = *session->Actions()->SkyReflectionSettings();
            settings->SkyReflectionTexture(*res->Result());
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}

```

请注意，上面使用了 `LoadTextureFromSASAsync` 变体，因为加载了内置纹理。 如果纹理是从[关联的 Blob 存储](../../how-tos/create-an-account.md#link-storage-accounts)加载的，请使用 `LoadTextureAsync` 变体。

## <a name="sky-texture-types"></a>天空纹理类型

可以使用[立体地图](https://en.wikipedia.org/wiki/Cube_mapping)和 *2D 纹理*作为环境地图。

所有纹理都必须采用[支持的纹理格式](../../concepts/textures.md#supported-texture-formats)。 不需要为天空纹理提供 mipmap。

### <a name="cube-environment-maps"></a>立体环境地图

下面提供了一个已解包的立体地图供参考：

![已解包的立体地图](media/Cubemap-example.png)

将 `AzureSession.Actions.LoadTextureAsync`/ `LoadTextureFromSASAsync` 与 `TextureType.CubeMap` 一起使用来加载立体地图纹理。

### <a name="sphere-environment-maps"></a>球体环境地图

使用 2D 纹理作为环境地图时，图像必须处于[球面坐标空间](https://en.wikipedia.org/wiki/Spherical_coordinate_system)中。

![球面坐标中的天空图像](media/spheremap-example.png)

结合使用 `TextureType.Texture2D` 和 `AzureSession.Actions.LoadTextureAsync` 加载球体环境地图。

## <a name="built-in-environment-maps"></a>内置环境地图

Azure 远程渲染提供了几个始终可用的内置环境地图。 所有内置环境地图都是立体地图。

|标识符                         | 说明                                              | 图示                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 各种条带光线，明亮的室内基本照明    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | 明亮的室内光线设置，多窗口光线      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | 中等亮度室内照明设置中的各种彩色光线  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | 适中亮度的大厅环境光线                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 昏暗室内设置和明暗对比度              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 白天的室内环境光线，明亮的窗口区域光线     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | 夜晚昏暗的天空和地面以及许多周边光线   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | 明亮的日光和阴影对比度                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 晴朗天空光线和适中的地面照明            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 适度变化的日光和阴影                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 傍晚接近黄昏时的日落光线                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明亮、嫩绿和白色光线色调，昏暗的地面 | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 白天的明亮地面环境光线                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | 与 TearsOfSteelBridge 相同                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>后续步骤

* [光线](../../overview/features/lights.md)
* [材料](../../concepts/materials.md)
* [纹理](../../concepts/textures.md)
* [TexConv 命令行工具](../../resources/tools/tex-conv.md)
