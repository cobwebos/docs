---
title: 用于搜索索引的 Azure SQL 托管实例连接
titleSuffix: Azure Cognitive Search
description: 启用公共终结点，以允许从 Azure 认知搜索上的索引器连接到 SQL 托管实例。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964883"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>配置从 Azure 认知搜索索引器到 SQL 托管实例的连接

如[使用索引器将 AZURE SQL 数据库连接到 azure 认知搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)中所述，Azure 认知搜索通过公共终结点为**SQL 托管实例**创建索引器。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>通过公共终结点创建 Azure SQL 托管实例
创建 SQL 托管实例，并选择 "**启用公共终结点**" 选项。

   ![启用公共终结点](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "启用公共终结点")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>启用 Azure SQL 托管实例公共终结点
你还可以在 "**安全** > "**虚拟网络** > " > **启用**"**公共终结点**托管实例上启用公共终结点。

   ![启用公共终结点](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "启用公共终结点")

## <a name="verify-nsg-rules"></a>验证 NSG 规则
检查网络安全组是否具有允许从 Azure 服务进行连接的正确**入站安全规则**。

   ![NSG 入站安全规则](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 入站安全规则")

> [!NOTE]
> 索引器仍要求使用公用终结点配置 SQL 托管实例以便读取数据。
> 但是，你可以选择使用以下2个规则替换当前规则（`public_endpoint_inbound`）来限制对该公共终结点的入站访问：
>
> * 允许来自 `AzureCognitiveSearch`[服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)（"SOURCE" = `AzureCognitiveSearch`，"NAME" = `cognitive_search_inbound`）的入站访问
>
> * 允许来自搜索服务的 IP 地址的入站访问，可通过 ping 其完全限定的域名（例如 `<your-search-service-name>.search.windows.net`）来获取。 （"SOURCE" = `IP address`，"NAME" = `search_service_inbound`）
>
> 对于这两个规则中的每一个，请设置 "PORT" = `3342`，"PROTOCOL" = `TCP`，"DESTINATION" = `Any`，"ACTION" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>获取公共终结点连接字符串
请确保使用**公用终结点**（端口3342，而不是端口1433）的连接字符串。

   ![公共终结点连接字符串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公共终结点连接字符串")

## <a name="next-steps"></a>后续步骤
在配置外，现在可以使用门户或 REST API 将 SQL 托管实例指定为 Azure 认知搜索索引器的数据源。 有关详细信息，请参阅[将 AZURE SQL 数据库连接到 azure 认知搜索使用索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。
