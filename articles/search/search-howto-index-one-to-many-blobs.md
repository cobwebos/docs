---
title: 通过 Azure Blob 索引器为包含多个搜索索引文档的 Blob 编制索引以进行全文搜索 - Azure 搜索
description: 使用 Azure 搜索 Blob 索引器抓取 Azure Blob 以获取文本内容。 每个 Blob 可能包含一个或多个 Azure 搜索索引文档。
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182305"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>为生成多个搜索文档的 Blob 编制索引
默认情况下，Blob 索引器将一个 Blob 的内容视为单个搜索文档。 某些 **parsingMode** 值支持单个 Blob 导致多个搜索文档的方案。 允许索引器从一个 Blob 提取多个搜索文档的不同类型的 **parsingMode** 包括：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一对多文档键
Azure 搜索索引中显示的每个文档由一个文档键唯一标识。 

如果未指定分析模式，并且索引中的键字段不存在显式映射，Azure 搜索会自动将 `metadata_storage_path` 属性[映射](search-indexer-field-mappings.md)为键。 这种映射确保每个 Blob 显示为不同的搜索文档。

使用上面所列的任一分析模式时，一个 Blob 将映射到“多个”搜索文档，因此，一个文档键仅基于 Blob 元数据是不适当的。 为了克服这种约束，Azure 搜索能够为从 Blob 提取的每个单个实体生成“一对多”的文档键。 此属性名为 `AzureSearch_DocumentKey`，将添加到从 Blob 提取的每个实体。 系统保证此属性的值对于各 Blob 中的每个实体唯一，而实体将显示为独立的搜索文档。

默认情况下，如果未指定键索引字段的显式字段映射，系统会使用 `base64Encode` 字段映射函数将 `AzureSearch_DocumentKey` 映射到该字段。

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

此设置会生成包含以下信息的 Azure 搜索索引（为简洁起见，base64 编码的 ID 已缩短）

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

但是，此映射不会生成索引中显示的 4 个文档，因为 `recordid` 字段在各 Blob 中不是唯一的。 因此，我们建议对“一对多”分析模式，应用从 `AzureSearch_DocumentKey` 属性到键索引字段的隐式字段映射。

如果确实想要设置显式字段映射，请确保 _sourceField_ 对于**所有 Blob** 中的每个实体都是不同的。

> [!NOTE]
> `AzureSearch_DocumentKey` 用来确保每个提取实体的唯一性的方法可能会发生变化，因此你不应该依赖于使用其值来解决应用程序的需求。

## <a name="see-also"></a>请参阅

+ [Azure 搜索中的索引器](search-indexer-overview.md)
+ [使用 Azure 搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)
+ [使用 Azure 搜索 Blob 索引器为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)
+ [使用 Azure 搜索 Blob 索引器为 JSON Blob 编制索引](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>后续步骤
* 若要详细了解 Azure 搜索，请参阅[搜索服务页](https://azure.microsoft.com/services/search/)。