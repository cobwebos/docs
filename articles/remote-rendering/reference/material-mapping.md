---
title: 模型格式的材料映射
description: 描述从模型源格式到 .PBR 材料的默认转换
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893080"
---
# <a name="material-mapping-for-model-formats"></a>模型格式的材料映射

将源资产[转换为模型](../how-tos/conversion/model-conversion.md)时，转换器将为每个[网格](../concepts/meshes.md)创建[材料](../concepts/materials.md)。 可以 [覆盖](../how-tos/conversion/override-materials.md)创建材料的方式。 但默认情况下，转换将创建 [.pbr 物料](../overview/features/pbr-materials.md)。 由于每个源文件格式（如 FBX）使用自己的约定来定义材料，因此必须将这些约定映射到 Azure 远程呈现的 .PBR 材料参数。 

本文列出了用于将材料从源资产转换为运行时材料的准确映射。

## <a name="gltf"></a>glTF

除了 *EmissiveFactor* 和 *EmissiveTexture*以外，Azure 远程呈现中几乎还支持 glTF 2.0 规范中的所有内容。

下表显示了映射：

| glTF | Azure 远程渲染 |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   程度                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   封闭                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode  |   alphaClipEnabled = false，isTransparent = false |
|   alphaMode    |   alphaClipEnabled = true，isTransparent = false  |
|   alphaMode   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

GlTF 中的每个纹理都可以具有 `texCoord` 值，这在 Azure 远程呈现材料中也受支持。

### <a name="embedded-textures"></a>嵌入的纹理

支持嵌入在* \* bin*或* \* glb*文件中的纹理。

### <a name="supported-gltf-extension"></a>支持的 glTF 扩展

除了基本功能集之外，Azure 远程呈现还支持以下 glTF 扩展：

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)：对应于 [颜色材料](../overview/features/color-materials.md)。 对于 *放射* 资料，建议使用此扩展。
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)：可以提供漫射反射度的纹理，而不是金属粗糙度的纹理。 Azure 远程呈现实现直接遵循扩展中的转换公式。

## <a name="fbx"></a>FBX

FBX 格式为关闭-源和 FBX 材料通常不与 .PBR 材料兼容。 FBX 使用包含许多唯一参数和属性的表面的复杂说明，而 **不是所有这些说明都由 Azure 远程呈现管道使用**。

> [!IMPORTANT]
> Azure 远程呈现模型转换管道仅支持 **FBX 2011 和更高版本**。

FBX 格式定义了用于材料的保守方法，但官方 FBX 规范中只有两种类型：

* *朗伯* -不常用于过去一段时间，但仍支持在转换时转换为冯氏。
* *冯氏* -几乎所有材料和大多数内容工具都使用此类型。

冯氏模型更为准确，它用作 FBX 材料的 *唯一* 模型。 下面将被称为 *FBX 材料*。

> Maya 为 FBX 使用两个自定义扩展插件，为类型的 .PBR 和 Stingray 类型定义自定义属性。 这些详细信息未包含在 FBX 规范中，因此当前不受 Azure 远程呈现支持。

FBX 材料使用 SpecularLevel 概念，因此，若要从漫射纹理转换为 albedo 地图，我们需要计算其他参数以将它们从漫射中减去。

> FBX 中的所有颜色和纹理都在 sRGB 空间中 (也称为伽玛空间) 但 Azure 远程呈现在可视化期间与线性空间一起使用，并且在框架结束时会将所有内容转换回 sRGB 空间。 Azure 远程呈现资产管道将所有内容转换为线性空间，以将其作为准备好的数据发送到呈现器。

此表显示了如何将纹理从 FBX 材料映射到 Azure 远程渲染材料。 其中一些部分不会直接使用，而是与参与公式的其他纹理组合在一起 (例如，漫射纹理) ：

| FBX | Azure 远程渲染 |
|:-----|:----|
| AmbientColor | 封闭映射   |
| DiffuseColor | *用于 Albedo、Metalness* |
| TransparentColor | *用于 Albedo 的 alpha 通道* |
| TransparencyFactor | *用于 Albedo 的 alpha 通道* |
| 不透明度 | *用于 Albedo 的 alpha 通道* |
| SpecularColor | *用于 Albedo、Metalness、粗糙度* |
| SpecularFactor| *用于 Albedo、Metalness、粗糙度* |
| ShininessExponent | *用于 Albedo、Metalness、粗糙度* |
| NormalMap | NormalMap |
| 平滑 | *转换为 NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

上面的映射是材料转换中最复杂的部分，因为有许多假设需要进行。 下面将讨论这些假设。

下面使用了一些定义：

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 +  `Specular` 。绿色∗ 0.7154 + `Specular` 。Blue ∗0.0721
* `DiffuseBrightness` = 0.299 * `Diffuse` 。红色<sup>2</sup> + 0.587 * `Diffuse` 。绿色<sup>2</sup> + 0.114 * `Diffuse` 。蓝<sup>2</sup>
* `SpecularBrightness` = 0.299 * `Specular` 。红色<sup>2</sup> + 0.587 * `Specular` 。绿色<sup>2</sup> + 0.114 * `Specular` 。蓝<sup>2</sup>
* `SpecularStrength` = 最大 (`Specular` 。红色， `Specular` 。绿色， `Specular` 。蓝) 

可从 [此处](https://en.wikipedia.org/wiki/Luma_(video))获取 SpecularIntensity 公式。
亮度公式在此 [规范](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)中进行了介绍。

### <a name="roughness"></a>粗糙度

`Roughness` 是 `Specular` `ShininessExponent` 使用 [此公式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)计算得出的。 该公式是冯氏反射指数中的粗糙度的近似值：

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness` 从 `Diffuse` glTF 规范计算，并 `Specular` 使用此 [公式](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)。

这里的思路是解决公式： Ax<sup>2</sup> + Bx + C = 0。
基本上，dielectric 的表面以反光的形式反映了约4% 的光线，而其余表面则为漫射。 金属表面以漫射方式反映没有任何灯光，但以镜面方式呈现。
此公式具有几个缺点，因为没有办法区分光面纸和光滑表面。 在大多数情况下，图面都具有金属属性，因此光滑的塑料/橡胶面看起来可能不正常。

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

### <a name="albedo"></a>Albedo

`Albedo` 是从 `Diffuse` 、 `Specular` 和计算的 `Metalness` 。

如 "Metalness" 一节中所述，dielectric 的表面反映了约4% 的光线。  
这里的思路是 `Dielectric` `Metal` 使用 `Metalness` 值作为系数，以线性方式在和颜色之间进行插入。 如果 metalness 为 `0.0` ，则根据反光，如果镜面为高) ，则它将为暗色 (; 否则，如果不) 存在反射，则不会更改 (。 如果 metalness 是一个较大的值，则漫射色将会消失，以支持反射颜色。

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` 已由以上公式计算，但 alpha 通道需要其他计算。 FBX 格式与透明度无关，有多种方法可以定义它。 不同的内容工具使用不同的方法。 这里的思路是将它们统一为一个公式。 但如果不是以常见方式创建的，则它会使某些资产错误地显示为透明。

此计算从 `TransparentColor` 、 `TransparencyFactor` 、 `Opacity` ：

如果 `Opacity` 定义了，则直接使用： `AlbedoAlpha`  =  `Opacity` else  
如果 `TransparencyColor` 定义了，则 `AlbedoAlpha` = 1.0- ( # B1 `TransparentColor` 。红色 + `TransparentColor` 。绿色 + `TransparentColor` 。蓝) /3.0) else  
如果 `TransparencyFactor` 为，则 `AlbedoAlpha` = 1.0- `TransparencyFactor`

最终 `Albedo` 颜色具有四个通道，并将 `AlbedoRGB` 与组合在一起 `AlbedoAlpha` 。

### <a name="summary"></a>总结

这里汇总了， `Albedo` `Diffuse` 如果接近于零，则会非常接近原始 `Specular` 。 否则，表面看起来像是一个金属面，因而会丢失漫射色。 如果 `ShininessExponent` 足够大且非常明亮，则图面看起来更精美并更具反射 `Specular` 。 否则，表面看起来会很粗糙，几乎不会反映出环境。

### <a name="known-issues"></a>已知问题

* 当前公式不适用于简单的彩色几何。 如果 `Specular` 非常明亮，则所有几何都将成为没有任何颜色的反射金属表面。 此处的解决方法是 `Specular` 从原始中降低到30%，或者使用转换设置 [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)。
* 最近向 `Maya` 和 `3DS Max` 内容创建工具中添加了 .pbr 材料。 它们使用自定义的用户定义的黑盒属性将其传递到 FBX。 Azure 远程呈现不会读取这些附加属性，因为没有记录这些属性并且格式为关闭源。

## <a name="next-steps"></a>后续步骤

* [模型转换](../how-tos/conversion/model-conversion.md)
* [在模型转换过程中覆盖材料](../how-tos/conversion/override-materials.md)
