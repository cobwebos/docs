---
title: 排查常见的搜索索引器问题
titleSuffix: Azure Cognitive Search
description: 修复 Azure 认知搜索中索引器的错误和常见问题，包括数据源连接、防火墙和丢失的文档。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190936"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>排查 Azure 认知搜索中的常见索引器问题

在将数据索引到 Azure 认知搜索中时，索引器可能会遇到许多问题。 故障的主要类别包括：

* [连接到数据源或其他资源](#connection-errors)
* [文档处理](#document-processing-errors)
* [目标为索引的文档引入](#index-errors)

## <a name="connection-errors"></a>连接错误

> [!NOTE]
> 索引器对访问受 Azure 网络安全机制保护的数据源和其他资源的支持有限。 目前，索引器只能通过相应的 IP 地址范围限制机制或 NSG 规则（如果适用）访问数据源。 有关访问每个受支持数据源的详细信息，请参阅下文。
>
> 您可以通过 ping 其完全限定的域名（例如 ， `<your-search-service-name>.search.windows.net`） 来查找搜索服务的 IP 地址。
>
> 您可以使用[可下载的 JSON](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)文件`AzureCognitiveSearch`或通过[服务标记发现 API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)找到[服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)的 IP 地址范围。 IP 地址范围每周更新一次。

### <a name="configure-firewall-rules"></a>配置防火墙规则

Azure 存储、CosmosDB 和 Azure SQL 提供了可配置的防火墙。 防火墙启用后，没有具体的错误消息。 通常，防火墙错误是通用的，看起来像`The remote server returned an error: (403) Forbidden``Credentials provided in the connection string are invalid or have expired`或 。

在这样的实例中，有两个选项允许索引器访问这些资源：

* 通过允许**从所有网络**访问（如果可行），禁用防火墙。
* 或者，您可以在资源的防火墙规则（IP 地址范围限制）中允许访问搜索服务的 IP`AzureCognitiveSearch`地址[和服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)的 IP 地址范围。

有关为每个数据源类型配置 IP 地址范围限制的详细信息，请参阅以下链接：

* [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**限制**：如上述 Azure 存储文档中所述，仅当搜索服务和存储帐户位于不同区域时，IP 地址范围限制才起作用。

Azure 函数（可用作自定义 Web [Api 技能](cognitive-search-custom-skill-web-api.md)）也支持[IP 地址限制](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions)。 要配置的 IP 地址列表将是搜索服务的 IP 地址和服务标记的`AzureCognitiveSearch`IP 地址范围。

[此处](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)概述了在 Azure VM 上访问 SQL 服务器中数据的详细信息

### <a name="configure-network-security-group-nsg-rules"></a>配置网络安全组 （NSG） 规则

在 SQL 托管实例中访问数据或将 Azure VM 用作[自定义 Web Api 技能](cognitive-search-custom-skill-web-api.md)的 Web 服务 URI 时，客户无需关注特定的 IP 地址。

在这种情况下，可以将 Azure VM 或 SQL 托管实例配置为驻留在虚拟网络中。 然后，可以配置网络安全组来筛选可流入和流出虚拟网络子网和网络接口的网络流量类型。

`AzureCognitiveSearch`服务标记可以直接用于入站[NSG 规则](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules)，而无需查找其 IP 地址范围。

[此处](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)概述了有关在 SQL 托管实例中访问数据的更多详细信息

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB"索引"未启用

Azure 认知搜索对 Cosmos DB 索引存在隐式依赖。 如果在 Cosmos DB 中关闭自动索引，Azure 认知搜索会返回成功状态，但无法索引容器内容。 有关如何查看设置和启用索引功能的说明，请参阅[管理 Azure Cosmos DB 中的索引编制](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)。

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

* 文档仅包含扫描的图像。 包含扫描图像 (JPG) 之类的非文本内容的 PDF Blob 不会在标准 Blob 索引管道中生成结果。 如果您有带有文本元素的图像内容，则可以使用[认知搜索](cognitive-search-concept-image-scenarios.md)来查找和提取文本。
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
* 在数据源中指定的 [query](/rest/api/searchservice/create-data-source) 排除了该文档。 索引器不能索引不属于数据源的文档。
* [字段映射](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)或[AI 扩充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)已更改文档，它看起来与您预期的不同。
* 使用[查找文档 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 来查找文档。
