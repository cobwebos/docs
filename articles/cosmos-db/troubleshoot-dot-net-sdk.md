---
title: 诊断和排查使用 Azure Cosmos DB .NET SDK 时遇到的问题
description: 使用客户端日志记录等功能和其他第三方工具来确定、诊断和排查使用 .NET SDK 时遇到的 Azure Cosmos DB 问题。
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932560"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>诊断和排查使用 Azure Cosmos DB .NET SDK 时遇到的问题
本文介绍了将 [.NET SDK](sql-api-sdk-dotnet.md) 与 Azure Cosmos DB SQL API 帐户配合使用时的常见问题、解决方法、诊断步骤和工具。
.NET SDK 提供客户端逻辑表示用于访问 Azure Cosmos DB SQL API。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

## <a name="checklist-for-troubleshooting-issues"></a>问题排查清单：
在将应用程序投放生产之前，请查看以下清单。 使用该清单有助于防止出现多个常见问题。 出现问题时可以快速诊断：

*   使用最新的 [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)。 不应该将预览版 SDK 用于生产。 这可以防止出现已予以修复的已知问题。
*   查看[性能提示](performance-tips.md)并按照建议的做法进行操作。 这有助于防止缩放、延迟和其他性能问题。
*   启用 SDK 日志记录以帮助排查问题。 启用日志记录可能会影响性能，因此最好仅在解决问题时才启用日志记录。 可以启用以下日志：
    *   使用 Azure 门户[记录指标](monitor-accounts.md)。 门户指标显示 Azure Cosmos DB 遥测，这有助于确定此问题是与 Azure Cosmos DB 相对应还是来自客户端。
    *   记录点操作响应中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)。
    *   记录所有查询响应中的 [SQL 查询指标](sql-api-query-metrics.md) 
    *   遵循有关 [SDK 日志记录]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的设置

请查看本文中的[常见问题和解决方法](#common-issues-workarounds)部分。

查看我们积极关注的 [GitHub 问题部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 检查是否已提交包含解决方法的任何类似问题。 如果找不到解决方法，请提出 GitHub 问题。 对于紧急问题，可以开具支持票证。


## <a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="general-suggestions"></a>常规建议
* 尽可能在 Azure Cosmos DB 帐户所在的同一个 Azure 区域中运行应用。 
* 由于客户端计算机上的资源不足，你可能会遇到连接/可用性问题。 我们建议监视运行 Azure Cosmos DB 客户端的节点上的 CPU 利用率，如果这些节点的负载较大，请纵向/横向扩展节点。

### <a name="check-the-portal-metrics"></a>检查门户指标
检查[门户指标](monitor-accounts.md)有助于确定问题是否与客户端相关，或者服务是否有问题。 例如，如果指标中包含较高比率的速率受限请求（HTTP 状态代码 429，表示请求受到限制），请查看[请求速率过大]部分。 

### <a name="request-timeouts"></a>请求超时
使用直接/TCP 连接时往往会发生 RequestTimeout，但在网关模式下也可能会发生。 下面是常见的已知原因，以及有关如何解决问题的建议。

* CPU 利用率较高，导致延迟和/或请求超时。 客户可以纵向扩展主机以便为其提供更多的资源，或者可将负载分散到更多的计算机。
* 套接字/端口可用性可能较低。 在 Azure 中运行时，使用 .NET SDK 的客户端可以访问 Azure SNAT （PAT）端口耗尽。 若要减少出现此问题的机率，请使用最新版本的 .NET SDK。 这是为什么建议始终运行最新 SDK 版本的示例。
* 创建多个 DocumentClient 实例可能会导致连接争用和超时问题。 遵循[性能提示](performance-tips.md)，并在整个进程中使用单个 DocumentClient 实例。
* 用户有时会看到延迟或请求超时增大，原因是集合的预配不足、后端限制了请求，或者客户端在不提示调用方的情况下在内部重试。 检查[门户指标](monitor-accounts.md)。
* Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 请检查门户指标，以确定工作负荷是否遇到了热[分区键](partition-data.md)。 热分区键会导致消耗的聚合吞吐量 (RU/s) 看上去低于预配的 RU，但单个分区消耗的吞吐量 (RU/s) 会超过预配的吞吐量。 
* 此外，2.0 SDK 将通道语义添加到了直接/TCP 连接。 同时为多个请求使用一个 TCP 连接。 在特定的情况下，这可能会导致两个问题：
    * 高并发度可能导致通道中出现争用。
    * 大的请求或响应可能导致通道上出现行首阻塞，加剧资源争用，甚至出现相对较低的并发度。
    * 如果遇到上述两种情况之一（或者怀疑 CPU 利用率较高），可能的缓解措施如下：
        * 尝试纵向/横向扩展应用程序。
        * 此外，可以通过[跟踪侦听器](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)捕获 SDK 日志，以获取更多详细信息。

### <a name="connection-throttling"></a>连接限制
主机上的连接受限可能导致连接限制。 在版本低于 2.0 的 SDK 中，Azure 中运行的客户端可能会遇到 [Azure SNAT (PAT) 端口耗尽]。

### <a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在没有公共 IP 地址的 Azure 虚拟机上，则默认情况下，[Azure SNAT 端口](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)用于建立与 VM 外部任何终结点的连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)的限制。

 仅当 VM 具有专用 IP 地址且来自 VM 的进程尝试连接到公共 IP 地址时，才会使用 Azure SNAT 端口。 有两种解决方法可以避免 Azure SNAT 限制：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) 将子网添加到防火墙。
* 将公共 IP 分配给 Azure VM。

### <a name="http-proxy"></a>HTTP 代理
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

### 请求速率过大<a name="request-rate-too-large"></a>
“请求速率过大”或错误代码 429 表示请求正受到限制，因为消耗的吞吐量 (RU/s) 已超过预配的吞吐量。 SDK 会根据指定的[重试策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 检查[门户的指标](use-metrics.md)，查看是否收到429错误。 查看[分区键](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)，以确保均匀分配存储和请求量。 

### <a name="slow-query-performance"></a>查询性能较低
[查询指标](sql-api-query-metrics.md)有助于确定查询在哪个位置花费的时间最多。 在查询指标中，可以查看查询在客户端与后端上花费的时间。
* 如果后端查询的返回速度较快，并将大量的时间花费在客户端上，请检查计算机上的负载。 可能的原因是资源不足，SDK 正在等待提供可用的资源来处理响应。
* 如果后端查询速度较慢，请尝试[优化查询](optimize-cost-queries.md)，并查看当前的[索引策略](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[请求速率过大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) 端口耗尽]: #snat
[Production check list]: #production-check-list


