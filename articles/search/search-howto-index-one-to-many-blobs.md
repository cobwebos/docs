---
title: 包含多个文档的索引 blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 认知搜索 Blob 索引器抓取 Azure Blob 以获取文本内容，该索引器中的每个 blob 可能会生成一个或多个搜索索引文档。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: e5a69525c4bd0717c0561bc61ee3c52aa68e1c9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533955"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>为可以生成多个搜索文档的 Blob 编制索引
默认情况下，Blob 索引器将一个 Blob 的内容视为单个搜索文档。 某些 **parsingMode** 值支持单个 Blob 导致多个搜索文档的方案。 允许索引器从一个 Blob 提取多个搜索文档的不同类型的 **parsingMode** 包括：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一对多文档键
Azure 认知搜索索引中显示的每个文档由一个文档键唯一标识。 

如果未指定分析模式，并且索引中的键字段不存在显式映射，Azure 认知搜索会自动将 `metadata_storage_path` 属性[映射](search-indexer-field-mappings.md)为键。 这种映射确保每个 Blob 显示为不同的搜索文档。

使用上面所列的任一分析模式时，一个 Blob 将映射到“多个”搜索文档，因此，一个文档键仅基于 Blob 元数据是不适当的。 为了克服这种约束，Azure 认知搜索能够为从 Blob 提取的每个单个实体生成“一对多”的文档键。 此属性名为 `AzureSearch_DocumentKey`，将添加到从 Blob 提取的每个实体。 系统保证此属性的值对于各 Blob 中的每个实体唯一，而实体将显示为独立的搜索文档。 

默认情况下，如果未指定键索引字段的显式字段映射，系统会使用 `base64Encode` 字段映射函数将 `AzureSearch_DocumentKey` 映射到该字段。

## <a name="example"></a>示例
假设某个索引定义包含以下字段：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 容器包含采用以下结构的 Blob：

_Blob1.json_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.json_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

创建索引器并将 **parsingMode** 设置为 `jsonLines`（未指定键字段的任何显式字段映射）时，将隐式应用以下映射

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

此设置将导致 Azure 认知搜索索引，其中包含以下信息 (base64 编码的 ID 为简洁起见缩短) 

| ID | 温度 | 压力 | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引键字段的自定义字段映射

假设索引定义与前面的示例相同，并且 Blob 容器包含采用以下结构的 Blob：

_Blob1.json_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.json_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

使用 `delimitedText` **parsingMode** 创建索引器时，可能会自然而然地将字段映射函数设置为如下所示的键字段：

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

但是，此映射不会生成索引中显示的 4 个文档，因为 `recordid` 字段在各 Blob 中不是唯一的。   因此，我们建议对“一对多”分析模式，应用从 `AzureSearch_DocumentKey` 属性到键索引字段的隐式字段映射。

如果确实想要设置显式字段映射，请确保 _sourceField_ 对于**所有 Blob** 中的每个实体都是不同的。

> [!NOTE]
> `AzureSearch_DocumentKey` 用来确保每个提取实体的唯一性的方法可能会发生变化，因此你不应该依赖于使用其值来解决应用程序的需求。

## <a name="help-us-make-azure-cognitive-search-better"></a>帮助我们改善 Azure 认知搜索
如果有功能请求或改进建议，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 上提供相关意见。 如果需要使用现有功能的帮助，请在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上发布问题。

## <a name="next-steps"></a>后续步骤

如果尚未熟悉 blob 索引编制的基本结构和工作流，则应先[使用 Azure 认知搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)。 请查看以下文章，详细了解不同 blob 内容类型的分析模式。

> [!div class="nextstepaction"]
> [为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
> [为 JSON blob 编制索引](search-howto-index-json-blobs.md)
