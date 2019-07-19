---
title: 用于搜索索引的 azure SQL 托管实例连接-Azure 搜索
description: 启用公共终结点, 以允许从 Azure 搜索索引器连接到 SQL 托管实例。
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229123"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>配置从 Azure 搜索索引器到 SQL 托管实例的连接
如[使用索引器将 AZURE SQL 数据库连接到 Azure 搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)中所述, Azure 搜索通过公共终结点为**SQL 托管实例**创建索引器。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>通过公共终结点创建 Azure SQL 托管实例
创建 SQL 托管实例, 并选择 "**启用公共终结点**" 选项。

   ![启用公共终结点](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "启用公共终结点")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>启用 Azure SQL 托管实例公共终结点
你还可以在**安全** > **虚拟网络** > **公共终结点** > **启用**下的现有 SQL 托管实例上启用公共终结点。

   ![启用公共终结点](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "启用公共终结点")

## <a name="verify-nsg-rules"></a>验证 NSG 规则
检查网络安全组是否具有允许从 Azure 服务进行连接的正确**入站安全规则**。

   ![NSG 入站安全规则](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 入站安全规则")

## <a name="get-public-endpoint-connection-string"></a>获取公共终结点连接字符串
请确保使用**公用终结点**(端口 3342, 而不是端口 1433) 的连接字符串。

   ![公共终结点连接字符串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公共终结点连接字符串")

## <a name="next-steps"></a>后续步骤
在配置外, 现在可以使用门户或 REST API 将 SQL 托管实例指定为 Azure 搜索索引器的数据源。 有关详细信息，请参阅[使用索引器将 Azure SQL 数据库连接到 Azure 搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。
