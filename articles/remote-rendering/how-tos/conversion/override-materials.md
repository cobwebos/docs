---
title: 模型转换期间覆盖材料
description: 解释转换时材料重写工作流
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681475"
---
# <a name="override-materials-during-model-conversion"></a>模型转换期间覆盖材料

在转换过程中，源模型中的材料设置用于定义渲染器使用的[PBR 材质](../../overview/features/pbr-materials.md)。
有时，[默认转换](../../reference/material-mapping.md)不会提供所需的结果，您需要进行更改。
转换模型以在 Azure 远程渲染中使用时，可以提供材质覆盖文件，以自定义基于每个材质的材料转换方式。
有关[配置模型转换](configure-model-conversion.md)的部分具有声明材质重写文件名的说明。

## <a name="the-override-file-used-during-conversion"></a>转换期间使用的覆盖文件

作为一个简单的示例，假设一个框模型具有单个材质，称为"默认"。 需要调整色泽颜色以用于 ARR。
在这种情况下，可以按照如下`box_materials_override.json`方式创建文件：

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

该文件`box_materials_override.json`放置在输入容器中，并在 旁边`ConversionSettings.json``box.fbx`添加 ， 告诉转换在哪里可以找到重写文件（请参阅[配置模型转换](configure-model-conversion.md)）：

```json
{
    "material-override" : "box_materials_override.json"
}
```

转换模型后，将应用新设置。

### <a name="color-materials"></a>颜色材料

[彩色材质](../../overview/features/color-materials.md)模型描述了独立于照明的不断着色表面。
例如，这对于摄影测量算法所制作的资产很有用。
在材质覆盖文件中，材质可以通过设置为`unlit`声明为颜色材质。 `true`

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

### <a name="ignore-specific-texture-maps"></a>忽略特定的纹理贴图

有时，您可能希望转换过程忽略特定的纹理贴图。 当模型由生成渲染器无法正确理解的特殊贴图的工具生成时，可能就是这种情况。 例如，用于定义非数性以外的事物的"不一元性映射"或"正常映射"存储为"BumpMap"的模型。 （在后一种情况下，您希望忽略"正常映射"，这将导致转换器使用"BumpMap"作为"正常地图"。

原理很简单。 只需添加一个称为`ignoreTextureMaps`的属性并添加要忽略的任何纹理贴图：

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

有关可以忽略的纹理贴图的完整列表，请参阅下面的 JSON 架构。

## <a name="json-schema"></a>JSON 架构

此处给出了材质文件的完整 JSON 架构。 除`unlit`和`ignoreTextureMaps`外，可用的属性是[颜色材质](../../overview/features/color-materials.md)和[PBR 材质](../../overview/features/pbr-materials.md)模型部分中描述的属性的子集。

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

* [颜色材料](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)