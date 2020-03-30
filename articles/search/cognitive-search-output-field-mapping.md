---
title: 将输入映射到输出字段
titleSuffix: Azure Cognitive Search
description: 提取和丰富源数据字段，并映射到 Azure 认知搜索索引中的输出字段。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280968"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>如何将 AI 丰富的字段映射到可搜索索引

在本文中，你将了解如何将扩充输入字段映射到可搜索索引中的输出字段。 [定义技能组合](cognitive-search-defining-skillset.md)后，你必须将直接添加值的任何技能的输出字段映射到搜索索引中的给定字段。 

将内容从富集文档移动到索引中需要输出字段映射。  富集文档实际上是一个信息树，即使索引中支持复杂类型，有时您可能希望将信息从富集树转换为更简单的类型（例如，字符串数组）。 输出字段映射允许您通过拼平信息来执行数据形状转换。

## <a name="use-outputfieldmappings"></a>使用 outputFieldMappings
要映射字段，请按如下所示将 `outputFieldMappings` 添加到索引器定义：

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

请求正文的结构如下：

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

对于每个输出字段映射，设置富集文档树（sourceFieldName）中数据的位置，以及索引（目标字段名称）中引用的字段的名称。

## <a name="flattening-information-from-complex-types"></a>从复杂类型拼合信息 

sourceFieldName 中的路径可以表示一个元素或多个元素。 在上述示例中，```/document/content/sentiment``` 表示单个数字值，而 ```/document/content/organizations/*/description``` 表示多个组织说明。 

如果有多个元素，它们将“平展”成包含每个元素的数组。 

更具体地说，对于 ```/document/content/organizations/*/description``` 示例，**“描述”字段中的数据在编制索引之前将类似于说明的平面数组：

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

这是一个重要的原则，所以我们将提供另一个例子。 假设您有一个复杂类型的数组作为扩充树的一部分。 假设有一个称为 custom实体的成员具有类似下面描述的复杂类型的数组。

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

假设索引具有一个称为"疾病"类型的"疾病"的字段集合 （Edm.String），您希望在其中存储实体的每个名称。 

这可以通过使用""\*符号轻松完成，如下所示：

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

此操作将简单地将自定义实体元素的每个名称"拼平"到单个字符串数组中，如下所示：

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>后续步骤
将扩充字段映射到可搜索字段后，你可以设置每个可搜索字段中的字段属性[作为索引定义的一部分](search-what-is-an-index.md)。

有关字段映射的详细信息，请参阅[Azure 认知搜索索引器中的字段映射](search-indexer-field-mappings.md)。
