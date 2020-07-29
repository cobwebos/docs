---
title: 在模型转换期间替代材料
description: 介绍转换时重写工作流的材料
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681475"
---
# <a name="override-materials-during-model-conversion"></a>在模型转换期间替代材料

在转换过程中，源模型中的材料设置用于定义呈现器使用的[.pbr 材料](../../overview/features/pbr-materials.md)。
有时[默认转换](../../reference/material-mapping.md)不会提供所需的结果，需要进行更改。
转换模型以便在 Azure 远程呈现中使用时，可以提供材料覆盖文件来自定义材料转换如何按每个材料进行。
有关[配置模型转换](configure-model-conversion.md)的部分包含声明材料覆盖文件名的说明。

## <a name="the-override-file-used-during-conversion"></a>转换期间使用的替代文件

举个简单的例子，假设方框模型有一个称为 "默认" 的材料。 需要调整 albedo 颜色以在 ARR 中使用。
在这种情况下， `box_materials_override.json` 可以创建文件，如下所示：

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

该 `box_materials_override.json` 文件放置在输入容器中，并在 `ConversionSettings.json` 旁添加 `box.fbx` ，这会告诉转换在何处找到替代文件（请参阅[配置模型转换](configure-model-conversion.md)）：

```json
{
    "material-override" : "box_materials_override.json"
}
```

转换模型时，将应用新设置。

### <a name="color-materials"></a>颜色材料

[颜色材料](../../overview/features/color-materials.md)模型描述了与光照无关的持续着色的图面。
例如，这对于 Photogrammetry 算法创建的资产很有用。
在 "材料覆盖文件" 中，通过将设置为，可以将材料声明为颜色材料 `unlit` `true` 。

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>忽略特定纹理映射

有时，您可能希望转换过程忽略特定纹理映射。 这种情况可能是由一个工具生成的，该工具生成的是由呈现器无法正确理解的特殊映射。 例如，用于定义不透明度的 "OpacityMap" 或 "NormalMap" 存储为 "BumpMap" 的模型。 （在后一种情况下，您需要忽略 "NormalMap"，这将导致转换器使用 "BumpMap" 作为 "NormalMap"。）

原则很简单。 只需添加一个名为的属性 `ignoreTextureMaps` ，并添加要忽略的纹理映射：

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

有关可忽略的纹理映射的完整列表，请参阅下面的 JSON 架构。

## <a name="json-schema"></a>JSON 架构

此处提供了材料文件的完整 JSON 架构。 除了 `unlit` 和之外 `ignoreTextureMaps` ，"可用" 属性还是 "[颜色材料](../../overview/features/color-materials.md)" 和 " [.pbr" 材料](../../overview/features/pbr-materials.md)模型各部分中描述的属性的子集。

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>后续步骤

* [有色材料](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)