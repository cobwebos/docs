---
title: 模型格式的材料映射
description: 描述从模型源格式到 PBR 材质的默认转换
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680383"
---
# <a name="material-mapping-for-model-formats"></a>模型格式的材料映射

当源资产[转换为模型时](../how-tos/conversion/model-conversion.md)，转换器为每个[网格](../concepts/meshes.md)创建[材质](../concepts/materials.md)。 材料的创建方式可以[被覆盖](../how-tos/conversion/override-materials.md)。 但是，默认情况下，转换将创建[PBR 材料](../overview/features/pbr-materials.md)。 由于每个源文件格式（如 FBX）都使用自己的约定来定义材质，因此这些约定必须映射到 Azure 远程呈现的 PBR 材质参数。 

本文列出了用于将材质从源资产转换为运行时材质的精确映射。

## <a name="gltf"></a>glTF

Azure 远程渲染中几乎支持 glTF 2.0 规范的所有内容，但*EeeeFactor*和*Eeee 纹理*除外。

下表显示了映射：

| glTF | Azure 远程渲染 |
|:-------------------|:--------------------------|
|   基本颜色因子   |   阿贝多色              |
|   基色纹理  |   阿尔贝多地图                |
|   金属因子    |   金属性                |
|   金属纹理   |   金属度图             |
|   粗糙度因子   |   粗糙 度                |
|   粗糙纹理  |   粗糙度地图             |
|   遮挡因子   |   闭塞                |
|   遮挡纹理  |   遮挡映射             |
|   正常纹理     |   正常地图                |
|   阿尔法库夫       |   阿尔法剪辑阈值       |
|   阿尔法模式.OPAQUE  |   AlphaClip 启用 = false，透明 = 假 |
|   阿尔法模式.MASK    |   AlphaClip 启用 = true，透明 = 假  |
|   阿尔法模式.BLEND   |   透明 = 真实     |
|   双面       |   是双面            |
|   发光因子    |   -                        |
|   电子纹理   |   -                        |

glTF 中的每个纹理都可以有一`texCoord`个值，Azure 远程呈现材质中也支持该值。

### <a name="embedded-textures"></a>嵌入式纹理

支持嵌入在*\*.bin*或*\*.glb*文件中的纹理。

### <a name="supported-gltf-extension"></a>支持的 glTF 扩展

除了基本功能集外，Azure 远程呈现还支持以下 glTF 扩展：

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit：](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)对应于[颜色材料](../overview/features/color-materials.md)。 对于*放流*材料，建议使用此扩展。
* [KHR_materials_pbrSpecularGlossiness：](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)而不是金属粗糙纹理，你可以提供漫反射镜光泽纹理。 Azure 远程呈现实现直接遵循扩展中的转换公式。

## <a name="fbx"></a>Fbx

FBX 格式是闭源的，FBX 材料通常与 PBR 材料不兼容。 FBX 使用具有许多唯一参数和属性的曲面的复杂描述，**并非所有曲面都由 Azure 远程呈现管道使用**。

> [!IMPORTANT]
> Azure 远程呈现模型转换管道仅支持**FBX 2011 及更高**版本。

FBX 格式定义了材料的保守方法，官方 FBX 规范中只有两种类型：

* *兰伯特*- 不是常用相当一段时间已经，但它仍然支持转换为Phong在转换时间。
* *Phong* - 几乎所有的材料和大多数内容工具都使用这种类型。

Phong 模型更加精确，用作 FBX 材料*的唯*一模型。 下面将称为*FBX材料*。

> Maya 通过定义材质的 PBR 和 Stingray 类型的自定义属性，为 FBX 使用两个自定义扩展。 这些详细信息不包括在 FBX 规范中，因此 Azure 远程呈现当前不支持它。

FBX 材料使用漫反射-镜面-镜面水平概念，因此，要从漫反射纹理转换为反光度贴图，我们需要计算其他参数以从漫反射中减去它们。

> FBX 中的所有颜色和纹理都位于 sRGB 空间（也称为 Gamma 空间），但 Azure 远程渲染在可视化期间使用线性空间，在帧末尾将所有内容转换回 sRGB 空间。 Azure 远程呈现资产管道将所有内容转换为线性空间，将其作为准备的数据发送到呈现器。

下表显示了如何将纹理从 FBX 材质映射到 Azure 远程渲染材质。 其中一些不是直接使用，而是与参与公式的其他纹理（例如漫反射纹理）结合使用：

| Fbx | Azure 远程渲染 |
|:-----|:----|
| 环境色 | 遮挡贴图   |
| 漫反射颜色 | *用于阿尔贝多， 金属度* |
| 透明色 | *用于阿尔贝多的阿尔法通道* |
| 透明度因素 | *用于阿尔贝多的阿尔法通道* |
| 不透明度 | *用于阿尔贝多的阿尔法通道* |
| 镜面颜色 | *用于阿尔贝多， 金属性， 粗糙度* |
| 镜面因子| *用于阿尔贝多， 金属性， 粗糙度* |
| 光泽指数 | *用于阿尔贝多， 金属性， 粗糙度* |
| 法线图 | 法线图 |
| 撞 | *转换为普通地图* |
| 发色颜色 | - |
| 发射因子 | - |
| 反射颜色 | - |
| 置换颜色 | - |

由于必须做出许多假设，上述映射是材料转换中最复杂的部分。 我们将在下面讨论这些假设。

下面使用一些定义：

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.红色 = 0.2125 = `Specular`.绿色 = 0.7154 * `Specular`蓝色 0.0721
* `DiffuseBrightness`[ 0.299 `Diffuse`] 。红色<sup>2</sup> = 0.587 * `Diffuse`。绿色<sup>2</sup> = 0.114 * `Diffuse`。蓝色<sup>2</sup>
* `SpecularBrightness`[ 0.299 `Specular`] 。红色<sup>2</sup> = 0.587 * `Specular`。绿色<sup>2</sup> = 0.114 * `Specular`。蓝色<sup>2</sup>
* `SpecularStrength`= 最大`Specular`（。红色`Specular`， .绿色， `Specular`.蓝色）

镜面强度公式[从这里](https://en.wikipedia.org/wiki/Luma_(video))获得。
亮度公式在此[规范](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)中描述。

### <a name="roughness"></a>粗糙 度

`Roughness`计算和使用`Specular``ShininessExponent`[此公式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)。 该公式是 Phong 镜面指数的粗糙度近似值：

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>金属性

`Metalness`从[glTF 规范](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)中`Diffuse`计算并使用`Specular`此公式。

这里的想法是，我们求解方程：Ax<sup>2</sup> + Bx + C = 0。
基本上，电介质表面以镜面方式反射大约4%的光，其余是漫反射的。 金属表面以漫反射方式反射没有光线，但全部以镜面方式反射。
此配方有几个缺点，因为无法区分光泽塑料和光泽金属表面。 我们假设大多数时候表面具有金属特性，因此光滑的塑料/橡胶表面可能看起来不按预期。
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>反照 率

`Albedo`从`Diffuse``Specular`和 计算。 `Metalness`

如"金属度"部分所述，电介质表面反射大约 4% 的光。  
这里的想法是使用`Dielectric``Metal``Metalness`值作为因子在颜色之间线性插值。 如果金属性为`0.0`，则根据镜面的不同，它将是深色（如果镜面高）或漫反射不会更改（如果没有镜面）。 如果金属度值较大，则漫反射颜色将消失，转而采用镜面颜色。

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`已按上述公式计算，但 Alpha 通道需要额外的计算。 FBX 格式对透明度含糊不清，并且有许多方法可以定义它。 不同的内容工具使用不同的方法。 这里的想法是将它们统一到一个公式中。 但是，如果某些资产不是以通用方式创建的，则会使某些资产错误地显示为透明。

这是从 计算的`TransparentColor``TransparencyFactor`： `Opacity`

如果`Opacity`被定义，然后直接使用它： `AlbedoAlpha`  =  `Opacity`  
如果`TransparencyColor`已定义，则`AlbedoAlpha`= 1.0`TransparentColor`- （（）。红色`TransparentColor`= 。绿色`TransparentColor`= .蓝色） / 3.0） 其他  
如果`TransparencyFactor` `AlbedoAlpha` = 1.0 -`TransparencyFactor`

最终`Albedo`颜色有四个通道，与`AlbedoRGB`结合`AlbedoAlpha`。

### <a name="summary"></a>总结

在这里总结，`Albedo`将非常接近原来的`Diffuse`，如果`Specular`接近零。 否则，表面将看起来像金属表面，并失去漫反射的颜色。 如果`ShininessExponent`足够大且明亮，表面看起来更抛光和`Specular`反射。 否则，表面看起来粗糙，几乎不能反映环境。

### <a name="known-issues"></a>已知问题

* 当前公式不适用于简单的彩色几何体。 如果`Specular`足够明亮，则所有几何体都变为无颜色的反射金属表面。 这里的解决方法是从原来的下降到`Specular`30%，或使用转换设置[fbx假设金属](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)。
* PBR 材料最近被`Maya`添加到和`3DS Max`内容创建工具中。 他们使用自定义用户定义的黑盒属性将其传递给 FBX。 Azure 远程呈现不会读取这些其他属性，因为它们没有记录，并且格式是闭源的。

## <a name="next-steps"></a>后续步骤

* [模型转换](../how-tos/conversion/model-conversion.md)
* [模型转换期间的覆盖材料](../how-tos/conversion/override-materials.md)
