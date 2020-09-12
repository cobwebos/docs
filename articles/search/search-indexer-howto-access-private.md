---
title: 通过专用终结点访问安全资源的索引器
titleSuffix: Azure Cognitive Search
description: 如何为索引器设置专用终结点，以与安全资源通信
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: bdf3863ae4683c6ef0e8dd3dd67a952bebf62d52
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463435"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>通过专用终结点访问安全资源

可以配置 Azure 资源 (（例如用作数据源) 的存储帐户），以便只能从特定的虚拟网络列表访问这些资源。 还可以将其配置为禁止任何 "公用网络" 访问。
客户可以请求 Azure 认知搜索创建 (出站) [专用终结点连接](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) ，以通过索引器安全访问来自此类数据源的数据。

## <a name="shared-private-link-resources-management-apis"></a>共享的专用链接资源管理 Api

Azure 认知搜索客户请求创建的专用终结点，用于访问 "安全" 资源称为 *共享的专用链接资源*。 客户对资源 (（例如存储帐户) ）的访问权限，这些资源将载入到 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)。

Azure 认知搜索通过搜索管理 API 提供的功能，可以 [创建或更新共享的专用链接资源](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)。 你将使用此 API 以及其他 *共享的专用链接资源* 管理 api，通过 Azure 认知搜索索引器配置对安全资源的访问权限。

与某些资源的专用终结点连接只能通过搜索管理 API (`2020-08-01-Preview`) （在下表中以 "预览" 标记表示）来创建。 不带 "预览" 标记的资源可以通过预览 API 和 GA API (来创建 `2020-08-01`) 

以下是可以从 Azure 认知搜索中创建出站专用终结点的 Azure 资源的列表。 `groupId` 下表中列出的需要完全使用 API 中 (区分大小写的) 来创建共享的专用链接资源。

| Azure 资源 | 组 ID |
| --- | --- |
| Azure 存储-Blob (或) ADLS 第2代 | `blob`|
| Azure 存储-表 | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL 数据库 | `sqlServer`|
| Azure Database for MySQL (预览版)  | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure 函数 (预览)  | `sites` |

还可以通过 [列表支持的 API](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported)查询支持出站专用终结点连接的 Azure 资源的列表。

出于本指南的目的，将使用 [ARMClient](https://github.com/projectkudu/ARMClient) 和 [Postman](https://www.postman.com/) 的混合来演示 REST API 调用。

> [!NOTE]
> 在本指南中，假设搜索服务的名称是__contoso-搜索__，后者位于订阅 id 为__00000000-0000-0000-0000-000000000000__的订阅的资源组__contoso__中。 此搜索服务的资源 id 将为 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

本指南的其余部分将演示如何配置 __contoso 搜索__ 服务，以便其索引器可以访问安全存储帐户中的数据 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>保护你的存储帐户

将存储帐户配置为 [仅允许来自特定子网的访问](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)。 通过 Azure 门户，如果选中此选项并将设置为空，则表示不允许来自任何虚拟网络的流量。

   ![虚拟网络访问](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "虚拟网络访问")

> [!NOTE]
> [受信任的 Microsoft 服务方法](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)可用于绕过此类存储帐户的虚拟网络或 IP 限制，并可让搜索服务访问存储帐户中的数据，如操作[方法指南](search-indexer-howto-access-trusted-service-exception.md)中所述。 但是，在使用此方法时，Azure 认知搜索与存储帐户之间的通信通过安全的 Microsoft 骨干网络通过存储帐户的公共 IP 地址进行。

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>步骤1：创建存储帐户的共享专用链接资源

进行以下 API 调用，请求 Azure 认知搜索创建到存储帐户的出站专用终结点连接

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

`create-pe.json`表示 API) 请求正文的文件 (的内容如下所示：

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`成功时返回响应-创建出站专用终结点的过程是长时间运行 (异步) 操作。 它涉及部署以下资源-

1. 使用状态中的专用 IP 地址分配的专用终结点 `"Pending"` 。 专用 IP 地址是从分配给搜索服务特定专用索引器执行环境的虚拟网络的地址空间中获得的。 获得专用终结点批准后，从 Azure 认知搜索到存储帐户的任何通信均源自专用 IP 地址和安全的专用链接通道。
2. 基于的资源类型的专用 DNS 区域 `groupId` 。 这将确保对专用资源的任何 DNS 查找都利用与专用终结点相关联的 IP 地址。

请确保为 `groupId` 要为其创建专用终结点的资源的类型指定正确的。 任何不匹配都将导致不成功的响应消息。

与所有异步 Azure 操作一样， `PUT` 调用将返回一个 `Azure-AsyncOperation` 标头值，如下所示：

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

此 URI 可以定期轮询以获取操作的状态。 建议等到共享的专用链接资源操作状态达到终端状态 (即 `succeeded`) ，然后再继续。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>步骤2a：为存储帐户批准专用终结点连接

> [!NOTE]
> 本部分使用 Azure 门户逐步完成到存储的专用终结点的审批流。 还可以使用通过存储资源提供程序 (RP) 提供的 [REST API](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) 。
>
> 其他提供程序（如 CosmosDB、Azure SQL server 等）还提供了类似的 RP Api 来管理专用终结点连接。

导航到 Azure 门户上的存储帐户的 "**专用终结点连接**" 选项卡。 应该会对专用终结点连接发出请求，并 (上一个 API 调用中的请求消息，) 异步操作 __成功__ 完成。

   ![私有终结点批准](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "私有终结点批准")

选择由 Azure 认知搜索创建的专用终结点 (使用 "专用终结点" 列按前面的 API 中指定的名称标识专用终结点连接) 并选择 "批准"，其中包含适当的消息 (消息不是重要的) 。 请确保专用终结点连接如下所示 (可以从1-2 分钟开始，以便在门户上更新状态) 

![私有终结点已批准](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "私有终结点已批准")

在批准专用终结点连接请求后，这意味着流量 *可以* 流经专用终结点。 批准专用终结点后，Azure 认知搜索会在为其创建的 DNS 区域中创建必要的 DNS 区域映射。

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>步骤2b：查询共享的专用链接资源的状态

 若要在批准后确认共享的专用链接资源已更新，请通过 [GET API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get)获取其状态。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

如果 `properties.provisioningState` 资源的为 `Succeeded` 且 `properties.status` 为，则 `Approved` 表示共享专用链接资源正常运行，并且可以将索引器配置为通过专用终结点进行通信。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>步骤3：将索引器配置为在专用环境中运行

> [!NOTE]
> 即使在批准专用终结点连接之前，也可以执行此步骤。 在批准专用终结点连接之前，任何尝试与安全 (资源（如存储帐户) ）进行通信的索引器都将处于暂时性故障状态。 将无法创建新的索引器。 一旦批准专用终结点连接，索引器就能够访问专用存储帐户。

1. [创建一个数据源，该数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 指向安全存储帐户和存储帐户中的相应容器。 下面显示了通过 Postman 执行的请求。
![创建数据源](media\search-indexer-howto-secure-access\create-ds.png "创建数据源")

2. 同样， [创建一个索引](https://docs.microsoft.com/rest/api/searchservice/create-index) ，并根据需要使用 REST API [创建技能组合](https://docs.microsoft.com/rest/api/searchservice/create-skillset) 。

3. [创建一个索引器，该索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 指向上面创建的数据源、索引和技能组合。 此外，通过将索引器的配置属性设置为，强制索引器在专用执行环境中 `executionEnvironment` 运行 `"Private"` 。
![创建索引器](media\search-indexer-howto-secure-access\create-idr.png "创建索引器")

应成功创建索引器，并且应从存储帐户通过专用终结点连接对内容进行进度索引。 可以通过 [索引器状态 API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)监视索引器的状态。

> [!NOTE]
> 如果已经有索引器，只需通过 [PUT API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 更新这些索引器即可将设置 `executionEnvironment` 为 `"Private"` 。

## <a name="troubleshooting-issues"></a>对问题进行故障排除

- 创建索引器时，如果创建失败并出现类似于 "数据源凭据无效" 的错误消息，则表示专用终结点连接尚未 *获得批准* 或不起作用。
使用 [GET API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get)获取共享的专用链接资源的状态。 如果已 *获得批准* ，请检查 `properties.provisioningState` 资源。 如果是 `Incomplete` ，这意味着资源的某些基本依赖项未能预配- `PUT` 重新发出请求以 "重新创建" 共享的专用链接资源，该资源应能解决问题。 可能需要重新批准-再次检查资源的状态以进行验证。
- 如果创建索引器时未设置其 `executionEnvironment` ，则索引器创建可能会成功，但其执行历史记录将显示索引器运行不成功。 应 [更新索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 以指定执行环境。
- 如果创建索引器时没有设置， `executionEnvironment` 并且该索引器成功运行，则意味着 Azure 认知搜索确定其执行环境是特定于搜索服务的 "专用" 环境。 但是，这可能会根据各种因素而变化， (索引器使用的资源、搜索) 服务上的负载等，并在以后发生故障时，我们强烈建议你将设置 `executionEnvironment` 为， `"Private"` 以确保它不会在将来失败。
- [配额和限制](search-limits-quotas-capacity.md) 确定可以创建多少共享的专用链接资源，以及依赖于搜索服务的 SKU。

## <a name="next-steps"></a>后续步骤

了解有关专用终结点的详细信息：

- [什么是专用终结点？](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [专用终结点所需的 DNS 配置](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)