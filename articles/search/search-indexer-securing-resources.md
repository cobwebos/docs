---
title: 安全访问索引器资源
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索中索引器的 Azure 数据访问的网络级安全选项的概念性概述。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 85446847e8ad77bc83eea657ab17268839e0b231
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949813"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>使用 Azure 网络安全功能访问数据源的索引器

在执行期间，azure 认知搜索索引器可以发出对各种 Azure 资源的出站调用。 本文介绍了在使用 IP 防火墙、专用终结点和其他网络级安全机制保护资源时索引器访问资源的概念。 下表列出了索引器在典型运行中可能访问的可能的资源类型。

| 资源 | 索引器运行中的用途 |
| --- | --- |
| Azure 存储 (blob、表、ADLS 第2代)  | 数据源 |
| Azure 存储 (blob，表)  | 技能集 (缓存已扩充的文档，并将知识存储投影存储)  |
|  (各种 Api Azure Cosmos DB)  | 数据源 |
| Azure SQL Database | 数据源 |
| Azure IaaS Vm 上的 SQL server | 数据源 |
| SQL 托管实例 | 数据源 |
| Azure Functions | 自定义 web api 技能的主机 |
| 认知服务 | 附加到技能组合，用于在超过20个可用文档限制的情况上向扩充计费 |

> [!NOTE]
> 附加到技能组合的认知服务资源用于根据执行的根据计费，并写入搜索索引。 它不用于访问认知服务 API。 从索引器的扩充管道到认知服务 API 的访问通过安全的信道进行，在此信道中，数据在传输过程中进行了强加密，永远不会存储在静态数据中。

客户可以通过 Azure 提供的多个网络隔离机制来保护这些资源。 除了认知服务资源之外，索引器还限制了访问所有其他资源的能力，即使它们在下表中列出的网络是隔离的。

| 资源 | IP 限制 | 专用终结点 |
| --- | --- | ---- |
| Azure 存储 (blob、表、ADLS 第2代)  | 仅当存储帐户和搜索服务在不同区域中时才受支持 | 支持 |
| Azure Cosmos DB-SQL API | 支持 | 支持 |
| Azure Cosmos DB-Cassandra、Mongo 和 Gremlin API | 支持 | 不支持 |
| Azure SQL 数据库 | 支持 | 支持 |
| Azure IaaS Vm 上的 SQL server | 支持 | 不适用 |
| SQL 托管实例 | 支持 | 不适用 |
| Azure Functions | 支持 | 支持，仅适用于 Azure 函数的某些 Sku |

> [!NOTE]
> 除了上面列出的选项之外，对于受网络保护的 Azure 存储帐户，客户可以利用 Azure 认知搜索是 [受信任的 Microsoft 服务](../storage/common/storage-network-security.md#trusted-microsoft-services)这一事实。 这意味着，如果在存储帐户上启用了相应的基于角色的访问控制，则特定搜索服务可以绕过存储帐户的虚拟网络或 IP 限制，并可以访问存储帐户中的数据。 详细信息可在操作 [方法指南](search-indexer-howto-access-trusted-service-exception.md)中找到。 如果存储帐户或搜索服务无法移到不同的区域，则可以使用此选项而不是 IP 限制路由。

选择索引器应使用哪些安全访问机制时，请考虑以下约束：

- 所有 Azure 资源都不支持[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
- 不能将搜索服务预配到特定虚拟网络-Azure 认知搜索不会提供此功能。
- 当索引器利用 (出站) 专用终结点来访问资源时，可能会收取额外的 [专用链接费用](https://azure.microsoft.com/pricing/details/search/) 。

## <a name="indexer-execution-environment"></a>索引器执行环境

Azure 认知搜索索引器能够有效地从数据源提取内容，并将根据添加到提取的内容，还可以选择在将结果写入搜索索引之前生成投影。 根据分配给索引器的责任数量，它可以在以下两种环境之一中运行：

- 专用于特定搜索服务的环境。 在此类环境中运行的索引器与其他工作负荷 (例如其他客户发起索引编制或查询工作负荷) 共享资源。 通常，只有不需要大量资源的索引器 (例如，不要使用在此环境中运行的技能组合) 。
- 承载大量资源的索引器（如具有技能组合的索引器）的多租户环境。 在此环境中运行的资源大量资源可提供最佳性能，同时确保搜索服务资源可用于其他工作负荷。 这种多租户环境由 Azure 认知搜索进行管理和保护，对客户无需额外付费。

对于任何给定的索引器运行，Azure 认知搜索确定运行索引器的最佳环境。

## <a name="granting-access-to-indexer-ip-ranges"></a>授予对索引器 IP 范围的访问权限

如果你的索引器尝试访问的资源仅限于一组特定的 IP 范围，则你需要展开该设置以包含可能从其发起索引器请求的 IP 范围。 如上所述，有两个可能的环境可用于运行索引器以及访问请求从中发起。 你需要添加 __这两种__ 环境的 IP 地址，才能使索引器访问工作。

- 若要获取搜索服务特定专用环境的 IP 地址，请 `nslookup` (或 `ping`) 搜索服务的完全限定域名 (FQDN) 。 例如，公有云中的搜索服务的 FQDN 将为 `<service-name>.search.windows.net` 。 Azure 门户中提供了此信息。
- 多租户环境的 IP 地址可通过 `AzureCognitiveSearch` 服务标记获得。 [Azure 服务标记](../virtual-network/service-tags-overview.md) 对每个服务都有一个已发布的 IP 地址范围-可通过 [发现 API (预览) ](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 或 [可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)获取。 在这两种情况下，IP 范围按区域划分，你只能选取为在其中预配搜索服务的区域分配的 IP 范围。

对于某些数据源，可以直接使用服务标记，而不是枚举 IP 范围的列表， (仍需要显式使用) 搜索服务的 IP 地址。 这些数据源通过设置 [网络安全组规则](../virtual-network/network-security-groups-overview.md)来限制访问，这种规则在本机上支持添加服务标记，这与 IP 规则（例如 Azure 存储、CosmosDB、azure SQL 等提供的 ip 规则不同） `AzureCognitiveSearch` 是支持直接使用服务标记（除了搜索服务 IP 地址）的数据源：

- [IaaS Vm 上的 SQL server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL 托管实例](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

有关详细信息，请参阅操作 [方法指南](search-indexer-howto-access-ip-restricted.md)。

## <a name="granting-access-via-private-endpoints"></a>通过专用终结点授予访问权限

索引器可以利用 [专用终结点](../private-link/private-endpoint-overview.md) 来访问资源，访问被锁定以选择虚拟网络或没有启用任何公共访问权限的资源。
此功能仅适用于付费服务，对创建的专用终结点的数量有限制。 " [Azure 搜索限制" 页](search-limits-quotas-capacity.md)中记录了有关限制的详细信息。

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>步骤1：创建安全资源的专用终结点

客户应调用搜索管理操作， [创建或更新 *共享的专用链接资源* API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) ，以便创建到其安全资源的专用终结点连接 (例如，存储帐户) 。 通过此 (出站) 专用终结点连接的流量将仅源自搜索服务特定 "专用" 索引器执行环境中的虚拟网络。

Azure 认知搜索将验证此 API 的调用方是否有权批准对安全资源的专用终结点连接请求。 例如，如果请求与您无权访问的存储帐户建立专用终结点连接，则此调用将被拒绝。

### <a name="step-2-approve-the-private-endpoint-connection"></a>步骤2：批准专用终结点连接

当创建共享专用链接资源的 (异步) 操作完成时，将在 "挂起" 状态下创建专用终结点连接。 尚未通过连接流动流量。
然后，客户需要在其安全资源上查找此请求，并 "批准"。 通常，可以通过门户或 [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)来完成此操作。

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>步骤3：强制索引器在 "专用" 环境中运行

已批准的专用终结点允许从搜索服务传出调用具有某种形式的网络级别访问限制 (例如，配置为仅从特定虚拟网络访问的存储帐户数据源) 成功。
这意味着，任何能够通过专用终结点访问此类数据源的索引器都将成功。
如果未批准专用终结点，或者如果索引器不利用专用终结点连接，则索引器运行将在中结束 `transientFailure` 。

若要使索引器能够通过专用终结点连接访问资源，必须将索引器的设置为，以 `executionEnvironment` `"Private"` 确保所有索引器运行都能够利用专用终结点。 这是因为专用终结点是在特定于搜索服务的专用环境内预配的。

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

操作 [方法指南](search-indexer-howto-access-private.md)中更详细地介绍了这些步骤。
将已批准的专用终结点用于资源后，设置为 *私有* 的索引器将尝试通过专用终结点连接获取访问权限。

### <a name="limits"></a>限制

为了确保搜索服务的最佳性能和稳定性，搜索服务 SKU 会将限制 () 以下维度：

- 可以设置为 *私有*的索引器的类型。
- 可创建的共享专用链接资源的数目。
- 可为其创建共享专用链接资源的不同资源类型的数目。

[服务限制](search-limits-quotas-capacity.md)中记录了这些限制。