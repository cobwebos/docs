---
title: 为全文搜索从 Azure Blob 索引器中索引一个 blob 到多个搜索索引文档
description: 使用 Azure 搜索 Blob 索引器对用于文本内容的 Azure blob 爬网。 每个 blob 可能会生成一个或多个 Azure 搜索索引文档。
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: d58be681c6a3e609712f9b0206de69f01d6a35f6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73178000"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>为生成多个搜索文档的 blob 编制索引
默认情况下，blob 索引器会将 blob 的内容视为单个搜索文档。 某些**parsingMode**值支持单个 blob 可能会导致多个搜索文档的情况。 允许索引器从 blob 中提取多个搜索文档的不同类型的**parsingMode**是：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一对多文档键
显示在 Azure 搜索索引中的每个文档都由文档键唯一标识。 

如果未指定任何分析模式，并且索引中的键字段没有显式映射，Azure 搜索会自动将 `metadata_storage_path` 属性[映射](search-indexer-field-mappings.md)为键。 此映射确保每个 blob 作为不同的搜索文档显示。

当使用以上列出的任何分析模式时，一个 blob 映射到 "多个" 搜索文档，只基于不合适的 blob 元数据生成一个文档键。 若要克服此限制，Azure 搜索可以为从 blob 中提取的每个单独实体生成 "一对多" 文档键。 此属性 `AzureSearch_DocumentKey` 命名，并添加到从 blob 中提取的每个单独实体。 此属性的值对于_跨 blob_的每个单独实体都是唯一的，实体将显示为单独的搜索文档。

默认情况下，如果未指定键索引字段的显式字段映射，则使用 `base64Encode` 字段映射函数将 `AzureSearch_DocumentKey` 映射到该字段。

## <a name="example"></a>示例
假设您有一个具有以下字段的索引定义：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 容器的 blob 具有以下结构：

_Blob1_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

创建索引器并将**parsingMode**设置为 `jsonLines`-不为键字段指定任何显式字段映射时，将隐式应用以下映射
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

此设置将导致 Azure 搜索索引包含以下信息（为简洁起见，将 base64 编码的 id 缩短）

| id | 温度 | 压力 | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00：00：00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00：00：00Z |
| aHR0 ...YjIuanNvbjsx | 第 | 第 | 2018-01-12T00：00：00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00：00：00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引键字段的自定义字段映射

假设你的 blob 容器具有以下结构的 blob，假设其索引定义如下：

_Blob1_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

使用 `delimitedText` **parsingMode**创建索引器时，可能会很自然地将字段映射函数设置为键字段，如下所示：

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

但是，此映射_不_会导致在索引中显示4个文档，因为 `recordid` 字段在_blob_中不是唯一的。 因此，建议将从 `AzureSearch_DocumentKey` 属性应用的隐式字段映射用于 "一对多" 分析模式的键索引字段。

如果确实要设置显式字段映射，请确保每**个实体的各个实体**的_sourceField_都是不同的。

> [!NOTE]
> 用于确保每个提取实体的唯一性的 `AzureSearch_DocumentKey` 方法可能会发生变化，因此不应依赖于应用程序的需求。

## <a name="next-steps"></a>后续步骤

如果你尚不熟悉 blob 索引的基本结构和工作流，则应首先查看[为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)。 有关不同 blob 内容类型的分析模式的详细信息，请参阅以下文章。

> [!div class="nextstepaction"]
> 为[JSON](search-howto-index-json-blobs.md) blob 编制索引 
> [索引 CSV blob](search-howto-index-csv-blobs.md)
