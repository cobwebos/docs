---
title: 获取转换的相关信息
description: 获取转换的相关信息
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576619"
---
# <a name="get-information-about-conversions"></a>获取转换的相关信息

## <a name="information-about-a-conversion-the-result-file"></a>有关转换的信息：结果文件

当转换服务转换资产时，它会将任何问题的摘要写入 "结果文件"。 例如，如果 `buggy.gltf` 转换文件，则输出容器将包含一个名为的文件 `buggy.result.json` 。

结果文件列出在转换过程中发生的所有错误和警告，并提供结果摘要，即 `succeeded` `failed` 或 `succeeded with warnings` 。
结果文件结构化为对象的 JSON 数组，其中每个对象都有一个 string 属性，该属性是、、、 `warning` `error` 和之一 `internal warning` `internal error` `result` 。 在或) 中，最多只能有一个错误 `error` `internal error` ，并且将始终有一个 (错误 `result` 。

## <a name="example-result-file"></a>示例 *结果* 文件

下面的示例描述了成功生成 arrAsset 的转换。 但是，由于缺少纹理，因此生成的 arrAsset 可能不是预期的。

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>有关转换后的模型的信息：信息文件

转换服务生成的 arrAsset 文件仅适用于呈现服务。 但有时，您可能希望在不启动渲染会话的情况下访问有关模型的信息。 为了支持此工作流，转换服务将 JSON 文件放在输出容器中的 arrAsset 文件旁边。 例如，如果转换了某个文件 `buggy.gltf` ，则输出容器将包含一个名 `buggy.info.json` 为 "已转换资产" 的文件 `buggy.arrAsset` 。 它包含有关源模型、转换后的模型以及转换本身的信息。

## <a name="example-info-file"></a>示例 *信息* 文件

下面是通过转换名为的文件而生成的示例 *信息* 文件 `buggy.gltf` ：

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>信息文件中的信息

### <a name="the-files-section"></a>" *文件* " 部分

本部分包含提供的文件名。

* `input`：源文件的名称。
* `output`：在用户已指定非默认名称的情况下，输出文件的名称。

### <a name="the-conversionsettings-section"></a>*ConversionSettings*部分

此部分保存模型转换时指定的 [ConversionSettings](configure-model-conversion.md#settings-file) 副本。

### <a name="the-inputinfo-section"></a>*InputInfo*部分

此部分记录有关源文件格式的信息。

* `sourceAssetExtension`：源文件的文件扩展名。
* `sourceAssetFormat`：源文件格式的说明。
* `sourceAssetFormatVersion`：源文件格式的版本。
* `sourceAssetGenerator`：生成源文件的工具的名称（如果可用）。

### <a name="the-materialoverrides-section"></a>*MaterialOverrides*部分

本部分提供有关在向转换服务提供了材料覆盖文件时的 [材料重](override-materials.md) 写的信息。
其中包含以下信息：
* `numOverrides`：从材料覆盖文件中读取的替代项的数目。
* `numOverriddenMaterials`：已重写的材料数。

### <a name="the-inputstatistics-section"></a>*InputStatistics*部分

本部分提供有关源场景的信息。 此部分中的值与创建源模型的工具中的等效值之间通常会有差异。 这种差异是预期的，因为在导出和转换步骤中将修改模型。

* `numMeshes`：网格部分的数量，其中每个部件都可以引用单个材料。
* `numFaces`：整个模型中的 _三角形_ 总数。 请注意，在转换过程中将会三角化网格。 此数字用于生成 [标准呈现服务器大小](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)的多边形限制。
* `numVertices`：整个模型中的顶点总数。
* `numMaterial`：整个模型中的总材料数。
* `numFacesSmallestMesh`：模型的最小网格中的三角形数。
* `numFacesBiggestMesh`：模型的最大网格中的三角形数。
* `numNodes`：模型场景图中的节点数。
* `numMeshUsagesInScene`：节点引用网格的次数。 多个节点可能引用同一网格。
* `maxNodeDepth`：场景图中节点的最大深度。

### <a name="the-outputinfo-section"></a>*OutputInfo*部分

此部分记录有关生成的输出的一般信息。

* `conversionToolVersion`：模型转换器的版本。
* `conversionHash`： ArrAsset 中可用于呈现的数据的哈希。 可用于了解当在同一文件上重新运行时，转换服务是否生成了不同的结果。

### <a name="the-outputstatistics-section"></a>*OutputStatistics*部分

此部分记录通过转换后的资产计算的信息。

* `numMeshPartsCreated`： ArrAsset 中的网格数。 它可以与 `numMeshes` 部分中的不同 `inputStatistics` ，因为实例化受转换过程的影响。
* `numMeshPartsInstanced`：在 arrAsset 中重复使用的网格数。
* `recenteringOffset`：在 `recenterToOrigin` [ConversionSettings](configure-model-conversion.md) 中启用选项时，此值为转换后的模型将转换回其原始位置的转换。
* `boundingBox`：模型的边界。

## <a name="deprecated-features"></a>已弃用的功能

转换服务将文件 `stdout.txt` 和 `stderr.txt` 输出容器写入到输出容器，并且这些文件是警告和错误的唯一来源。
这些文件现在已弃用。 相反，请使用 [结果文件](#information-about-a-conversion-the-result-file) 来实现此目的。

## <a name="next-steps"></a>后续步骤

* [模型转换](model-conversion.md)
* [配置模型转换](configure-model-conversion.md)
