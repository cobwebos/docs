---
title: 为全文搜索从 Azure Blob 索引器中索引一个 blob 到多个搜索索引文档
titleSuffix: Azure Cognitive Search
description: 使用 Azure 认知搜索 Blob 索引器对用于文本内容的 Azure blob 爬网。 每个 blob 可能会生成一个或多个搜索索引文档。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 863d38f6ea0f071a1c1a6678d025ec5b37a306dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466428"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>为生成多个搜索文档的 blob 编制索引
默认情况下，Blob 索引器将一个 Blob 的内容视为单个搜索文档。 某些 **parsingMode** 值支持单个 Blob 导致多个搜索文档的方案。 允许索引器从一个 Blob 提取多个搜索文档的不同类型的 **parsingMode** 包括：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一对多文档键
Azure 认知搜索索引中显示的每个文档都由文档键唯一标识。 

如果未指定任何分析模式，并且索引中没有键字段的显式映射，Azure 认知搜索会自动将 `metadata_storage_path` 属性[映射](search-indexer-field-mappings.md)为键。 这种映射确保每个 Blob 显示为不同的搜索文档。

使用上面所列的任一分析模式时，一个 Blob 将映射到“多个”搜索文档，因此，一个文档键仅基于 Blob 元数据是不适当的。 若要克服此限制，Azure 认知搜索可以为从 blob 中提取的每个单独实体生成 "一对多" 文档键。 此属性名为 `AzureSearch_DocumentKey`，将添加到从 Blob 提取的每个实体。 系统保证此属性的值对于各 Blob 中的每个实体唯一，而实体将显示为独立的搜索文档。

默认情况下，如果未指定键索引字段的显式字段映射，系统会使用 `AzureSearch_DocumentKey` 字段映射函数将 `base64Encode` 映射到该字段。

## <a name="example"></a>示例
假设某个索引定义包含以下字段：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 容器包含采用以下结构的 Blob：

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

创建索引器并将 **parsingMode** 设置为 `jsonLines`（未指定键字段的任何显式字段映射）时，将隐式应用以下映射
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

此设置将导致 Azure 认知搜索索引包含以下信息（为简洁起见，将 base64 编码的 id 缩短）

| id | 温度 | 压力 | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引键字段的自定义字段映射

假设索引定义与前面的示例相同，并且 Blob 容器包含采用以下结构的 Blob：

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

使用 `delimitedText` **parsingMode** 创建索引器时，可能会自然而然地将字段映射函数设置为如下所示的键字段：

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

但是，此映射不会生成索引中显示的 4 个文档，因为  _字段在各 Blob 中不是唯一的。_ `recordid` 因此，我们建议对“一对多”分析模式，应用从 `AzureSearch_DocumentKey` 属性到键索引字段的隐式字段映射。

如果确实想要设置显式字段映射，请确保 _sourceField_ 对于**所有 Blob** 中的每个实体都是不同的。

> [!NOTE]
> `AzureSearch_DocumentKey` 用来确保每个提取实体的唯一性的方法可能会发生变化，因此你不应该依赖于使用其值来解决应用程序的需求。

## <a name="next-steps"></a>后续步骤

如果你尚不熟悉 blob 索引的基本结构和工作流，则应首先查看[为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)。 有关不同 blob 内容类型的分析模式的详细信息，请参阅以下文章。

> [!div class="nextstepaction"]
> 为[JSON](search-howto-index-json-blobs.md) blob 编制索引
> [索引 CSV blob](search-howto-index-csv-blobs.md)
