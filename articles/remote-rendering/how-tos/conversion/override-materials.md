---
title: 在模型转换期间替代材料
description: 介绍转换时重写工作流的材料
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576636"
---
# <a name="override-materials-during-model-conversion"></a>在模型转换期间替代材料

源模型中的材料设置用于定义呈现器使用的 [.pbr 材料](../../overview/features/pbr-materials.md) 。
有时 [默认转换](../../reference/material-mapping.md) 不会提供所需的结果，需要进行更改。
转换模型以供在 Azure 远程呈现中使用时，可以提供一个材料覆盖文件，以自定义每个材料如何进行材料转换。
如果 `<modelName>.MaterialOverrides.json` 在输入模型旁边的输入容器中找到一个名为的文件 `<modelName>.<ext>` ，则该文件将用作材料覆盖文件。

## <a name="the-override-file-used-during-conversion"></a>转换期间使用的替代文件

举个简单的例子，假设方框模型有一个称为 "默认" 的材料。
此外，假设它的 albedo 颜色需要调整为在 ARR 中使用。
在这种情况下， `box.MaterialOverrides.json` 可以创建文件，如下所示：

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

`box.MaterialOverrides.json`文件放置在旁边的输入容器中 `box.fbx` ，该容器指示转换服务应用新设置。

### <a name="color-materials"></a>颜色材料

[颜色材料](../../overview/features/color-materials.md)模型描述了与光照无关的持续着色的图面。
例如，颜色材料适用于 Photogrammetry 算法创建的资产。
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

有时，您可能希望转换过程忽略特定纹理映射。 这种情况可能是由一个工具生成的，该工具生成的是由呈现器无法正确理解的特殊映射。 例如，用于定义不透明度的 "OpacityMap" 或 "NormalMap" 存储为 "BumpMap" 的模型。  (在后一种情况下，你希望忽略 "NormalMap"，这将导致转换器使用 "BumpMap" 作为 "NormalMap"。 ) 

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

### <a name="applying-the-same-overrides-to-multiple-materials"></a>将相同的替代应用于多个材料

默认情况下，当材料覆盖文件的名称与材料名称完全匹配时，将应用该条目。
由于相同的替代应该适用于多个材料，因此，您可以根据需要提供一个正则表达式作为条目名称。
该字段 `nameMatching` 具有默认值 `exact` ，但它可以设置为，以表明该 `regex` 条目应应用于每个匹配的材料。
所使用的语法与用于 JavaScript 的语法相同。 下面的示例演示了一个替代，适用于名称如 "Material2"、"Material01" 和 "Material999" 的材料。

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

在材料覆盖文件中，最多只有一个条目适用于单个材料。
如果存在精确匹配 (即缺少 `nameMatching` 或等于 `exact` 材料名称) ，则选择该条目。
否则，将选择文件中与材料名称匹配的第一个 regex 条目。

### <a name="getting-information-about-which-entries-applied"></a>获取有关应用了哪些项的信息

写入到输出容器的 [信息文件](get-information.md#information-about-a-converted-model-the-info-file) 将提供有关所提供的替代数以及已重写的材料数的信息。

## <a name="json-schema"></a>JSON 架构

此处提供了材料文件的完整 JSON 架构。 除了 `unlit` 和之外 `ignoreTextureMaps` ，"可用" 属性还是 " [颜色材料](../../overview/features/color-materials.md) " 和 " [.pbr" 材料](../../overview/features/pbr-materials.md) 模型各部分中描述的属性的子集。

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
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
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
