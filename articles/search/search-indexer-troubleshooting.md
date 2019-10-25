---
title: 排查常见搜索索引器问题
titleSuffix: Azure Cognitive Search
description: 解决 Azure 认知搜索中索引器的错误和常见问题，包括数据源连接、防火墙和缺少的文档。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793630"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure 认知搜索中的常见索引器问题疑难解答

在将数据编入 Azure 认知搜索时，索引器可能会遇到许多问题。 故障的主要类别包括：

* [连接到数据源](#data-source-connection-errors)
* [文档处理](#document-processing-errors)
* [目标为索引的文档引入](#index-errors)

## <a name="data-source-connection-errors"></a>数据源连接错误

### <a name="blob-storage"></a>Blob 存储

#### <a name="storage-account-firewall"></a>存储帐户防火墙

Azure 存储提供可配置的防火墙。 默认情况下，防火墙处于禁用状态，因此 Azure 认知搜索可以连接到存储帐户。

防火墙启用后，没有具体的错误消息。 通常情况下，防火墙错误类似于：`The remote server returned an error: (403) Forbidden`。

可以在[门户](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)中验证防火墙是否已启用。 唯一受支持的解决方法是通过选择允许从["所有网络"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)进行访问来禁用防火墙。

如果索引器没有附加的技能组合，则_可能会_尝试为搜索服务的 IP 地址[添加例外](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules)。 但是，这种情况不受支持，因此不能保证有效。

您可以通过对搜索服务的 FQDN （`<your-search-service-name>.search.windows.net`）来找出搜索服务的 IP 地址。

### <a name="cosmos-db"></a>Azure Cosmos DB

#### <a name="indexing-isnt-enabled"></a>索引未启用

Azure 认知搜索对 Cosmos DB 索引具有隐式依赖关系。 如果在 Cosmos DB 中关闭自动索引编制，Azure 认知搜索将返回成功状态，但无法为容器内容编制索引。 有关如何查看设置和启用索引功能的说明，请参阅[管理 Azure Cosmos DB 中的索引编制](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)。

## <a name="document-processing-errors"></a>文档处理错误

### <a name="unprocessable-or-unsupported-documents"></a>文档无法处理或不受支持

[显式支持可记录格式的 Blob 索引器文档](search-howto-indexing-azure-blob-storage.md#SupportedFormats)。 有时候，Blob 存储容器包含不受支持的文档。 而另一些时候，可能存在有问题的文档。 可以通过[更改配置选项](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)来避免停止这些文档上的索引器：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>缺少文档内容

Blob 索引器可[查找并提取容器中 Blob 的文本](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)。 提取文本时出现的一些问题包括：

* 文档仅包含扫描的图像。 包含扫描图像 (JPG) 之类的非文本内容的 PDF Blob 不会在标准 Blob 索引管道中生成结果。 如果图像内容具有文本元素，则可以使用[认知搜索](cognitive-search-concept-image-scenarios.md)来查找和提取文本。
* Blob 索引器配置为仅索引元数据。 若要提取内容，必须将 Blob 索引器配置为[提取内容和元数据](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>索引错误

### <a name="missing-documents"></a>缺少文档

索引器从[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)查找文档。 有时候，索引中似乎会缺失数据源中本应进行索引的文档。 之所以发生这些错误，有多种常见原因：

* 文档尚未进行索引。 查看门户中是否有成功的索引器运行。
* 文档在索引器运行之后已更新。 如果索引器已在[计划](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)之中，它最终会重新运行并选取该文档。
* 在数据源中指定的 [query](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) 排除了该文档。 索引器不能索引不属于数据源的文档。
* [字段映射](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)或[AI 扩充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)已更改文档，其外观与预期的不同。
* 使用[查找文档 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 来查找文档。
