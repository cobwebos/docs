---
title: 允许访问索引器 IP 范围
titleSuffix: Azure Cognitive Search
description: 操作方法指南，介绍如何设置 IP 防火墙规则，以便索引器可以访问。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: f485569caef285601d1dce7acd116f13675da83a
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950187"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>设置 IP 防火墙规则以启用索引器访问

Azure 资源上的 IP 防火墙规则（例如存储帐户、Cosmos DB 帐户和 Azure SQL 服务器）仅允许来自特定 IP 范围的流量访问数据。

本文介绍如何通过 Azure 门户为存储帐户配置 IP 规则，以便 Azure 认知搜索索引器能够安全地访问数据。 在特定于存储的情况下，可以将本指南直接翻译为其他 Azure 资源，这些资源也提供 IP 防火墙规则，用于保护对数据的访问。

> [!NOTE]
> 仅当存储帐户和搜索服务位于不同区域时，存储帐户的 IP 防火墙规则才有效。 如果安装程序不允许这样做，我们建议使用 [受信任的服务异常选项](search-indexer-howto-access-trusted-service-exception.md)。

## <a name="get-the-ip-address-of-the-search-service"></a>获取搜索服务的 IP 地址

获取搜索服务 (FQDN) 的完全限定的域名。 这将类似于 `<search-service-name>.search.windows.net` 。 可以通过在 "Azure 门户上查找" 搜索服务 "来确定 FQDN。

   ![获取服务 FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "获取服务 FQDN")

可以通过执行 `nslookup` (或 FQDN) 来获取搜索服务的 IP 地址 `ping` 。 这将是要添加到防火墙规则的 IP 地址之一。

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>获取 "AzureCognitiveSearch" 服务标记的 IP 地址范围

服务标记的 IP 地址范围 `AzureCognitiveSearch` 可通过 [发现 API (预览) ](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 或 [可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)获取。

对于本演练，假设搜索服务是 Azure 公有云，应下载 [Azure 公共 JSON 文件](https://www.microsoft.com/download/details.aspx?id=56519) 。

   ![下载 JSON 文件](media\search-indexer-howto-secure-access\service-tag.png "下载 JSON 文件")

在 JSON 文件中，假设搜索服务位于美国西部，则会在下面列出多租户索引器执行环境的 IP 地址列表。

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

对于/32 IP 地址，请删除 "/32" (52.253.133.74/32 > 52.253.133.74) ，可以逐字使用其他。

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>将 IP 地址范围添加到 IP 防火墙规则

向存储帐户的防火墙规则添加 IP 地址范围的最简单方法是通过 Azure 门户。 在门户中找到存储帐户，并导航到 "**防火墙和虚拟网络**" 选项卡。

   ![防火墙和虚拟网络](media\search-indexer-howto-secure-access\storage-firewall.png "防火墙和虚拟网络")

添加前面为搜索服务 IP (1 获取的三个 IP 地址，2表示 `AzureCognitiveSearch` 地址范围内的服务标记) ，并单击 "**保存**"

   ![防火墙 IP 规则](media\search-indexer-howto-secure-access\storage-firewall-ip.png "防火墙 IP 规则")

防火墙规则需要5-10 分钟才能获得更新，此后索引器将能够访问存储帐户中的数据。

## <a name="next-steps"></a>后续步骤

现在，你已经知道了如何获取两组 IP 地址来允许访问索引，接下来请使用以下链接更新某些常见数据源的 IP 防火墙规则。

- [配置 Azure 存储防火墙](../storage/common/storage-network-security.md)
- [配置 CosmosDB 的 IP 防火墙](../cosmos-db/firewall-support.md)
- [为 Azure SQL server 配置 IP 防火墙](../azure-sql/database/firewall-configure.md)