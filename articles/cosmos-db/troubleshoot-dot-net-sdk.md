---
title: 诊断和排查使用 Azure Cosmos DB .NET SDK 时遇到的问题
description: 使用客户端日志记录等功能和其他第三方工具来确定、诊断和排查使用 .NET SDK 时遇到的 Azure Cosmos DB 问题。
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137948"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>诊断和排查使用 Azure Cosmos DB .NET SDK 时遇到的问题
本文介绍在将[.NET SDK](sql-api-sdk-dotnet.md)与 Azure Cosmos DB SQL API 帐户一起使用时，常见问题、解决方法、诊断步骤和工具。
.NET SDK 提供客户端逻辑表示用于访问 Azure Cosmos DB SQL API。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

## <a name="checklist-for-troubleshooting-issues"></a>问题排查清单：
在将应用程序投放生产之前，请查看以下清单。 使用该清单有助于防止出现多个常见问题。 出现问题时可以快速诊断：

*    使用最新的 [SDK](sql-api-sdk-dotnet-standard.md)。 不应该将预览版 SDK 用于生产。 这可以防止出现已予以修复的已知问题。
*    查看[性能提示](performance-tips.md)并按照建议的做法进行操作。 这有助于防止缩放、延迟和其他性能问题。
*    启用 SDK 日志记录以帮助排查问题。 启用日志记录可能会影响性能，因此，最好是只在排查问题时才启用日志记录。 可以启用以下日志：
    *    使用 Azure 门户[记录指标](monitor-accounts.md)。 门户指标显示 Azure Cosmos DB 遥测数据，这有助于确定问题是否与 Azure Cosmos DB 相关，或者是否由客户端造成。
    *    从点操作响应在 V2 SDK 中记录[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)或在 V3 SDK 中的[诊断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)。
    *    记录所有查询响应中的 [SQL 查询指标](sql-api-query-metrics.md) 
    *    遵循有关 [SDK 日志记录]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的设置

请查看本文中的[常见问题和解决方法](#common-issues-workarounds)部分。

查看我们积极关注的 [GitHub 问题部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 检查是否已提交包含解决方法的任何类似问题。 如果找不到解决方法，请提出 GitHub 问题。 对于紧急问题，可以开具支持票证。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="general-suggestions"></a>常规建议
* 尽可能在 Azure Cosmos DB 帐户所在的同一个 Azure 区域中运行应用。 
* 由于客户端计算机上的资源不足，你可能会遇到连接/可用性问题。 我们建议监视运行 Azure Cosmos DB 客户端的节点上的 CPU 利用率，如果这些节点的负载较大，请纵向/横向扩展节点。

### <a name="check-the-portal-metrics"></a>检查门户指标
检查[门户指标](monitor-accounts.md)将有助于确定它是否是客户端问题，还是服务出现问题。 例如，如果指标包含高速率限制请求率（HTTP 状态代码 429），这意味着请求被限制，则检查[请求速率过大]部分。 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>请求超时
使用直接/TCP 连接时往往会发生 RequestTimeout，但在网关模式下也可能会发生。 这些错误是常见的已知原因，以及有关如何解决问题的建议。

* CPU 利用率较高，导致延迟和/或请求超时。 客户可以纵向扩展主机以便为其提供更多的资源，或者可将负载分散到更多的计算机。
* 套接字/端口可用性可能较低。 在 Azure 中运行时，使用 .NET SDK 的客户端可能会遇到 Azure SNAT (PAT) 端口耗尽的情况。 若要减少遇到此问题的机率，请使用最新版本 2.x 或 3.x 的 .NET SDK。 这是建议始终运行最新 SDK 版本的示例。
* 创建多个 DocumentClient 实例可能会导致连接争用和超时问题。 遵循[性能提示](performance-tips.md)，并在整个进程中使用单个 DocumentClient 实例。
* 用户有时会看到延迟提升或请求超时，因为它们的集合调配不足，后端限制请求，并且客户端在内部重试。 检查[门户指标](monitor-accounts.md)。
* Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 请检查门户指标，以确定工作负荷是否遇到了热[分区键](partition-data.md)。 热分区键会导致消耗的聚合吞吐量 (RU/s) 看上去低于预配的 RU，但单个分区消耗的吞吐量 (RU/s) 会超过预配的吞吐量。 
* 此外，2.0 SDK 将通道语义添加到了直接/TCP 连接。 同时为多个请求使用一个 TCP 连接。 在特定的情况下，这可能会导致两个问题：
    * 高并发度可能导致通道中出现争用。
    * 大的请求或响应可能导致通道上出现行首阻塞，加剧资源争用，甚至出现相对较低的并发度。
    * 如果遇到上述两种情况之一（或者怀疑 CPU 利用率较高），可能的缓解措施如下：
        * 尝试纵向/横向扩展应用程序。
        * 此外，可以通过[跟踪侦听器](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)捕获 SDK 日志，以获取更多详细信息。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高网络延迟
通过使用 V2 SDK 中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)或 V3 SDK 中的[诊断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)，可以识别高网络延迟。

如果不存在[超时](#request-timeouts)，并且诊断显示单个请求，其中高延迟在`ResponseTime`和`RequestStartTime`之间的差异上明显显示，例如 （>300 毫秒在此示例中）：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

此延迟可能有多种原因：

* 应用程序未与 Azure Cosmos 数据库帐户在同一区域中运行。
* [首选位置](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations)或[应用程序区域](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion)配置不正确，并尝试连接到应用程序当前运行所在的其他区域。
* 由于流量高，网络接口上可能存在瓶颈。 如果应用程序在 Azure 虚拟机上运行，则可能存在解决方法：
    * 请考虑使用[启用了加速网络的虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * [在现有虚拟机上启用加速网络](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 考虑使用[更高端的虚拟机](../virtual-machines/windows/sizes.md)。

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在 Azure[虚拟机上，没有公共 IP 地址](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)，则默认情况下[Azure SNAT 端口](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)建立与 VM 外部任何终结点的连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)的限制。 这种情况可能导致连接限制、连接关闭或上述[请求超时](#request-timeouts)。

 仅当 VM 具有专用 IP 地址连接到公共 IP 地址时，才会使用 Azure SNAT 端口。 有两种解决方法可以避免 Azure SNAT 限制（前提是您已在整个应用程序中使用单个客户端实例）：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](../virtual-network/virtual-networks-acl.md) 将子网添加到防火墙。
* 将[公共 IP 分配给 Azure VM](../load-balancer/load-balancer-outbound-connections.md#assignilpip)。

### <a name="http-proxy"></a>HTTP 代理
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>请求速率过大
"请求速率太大"或错误代码 429 表示您的请求被限制，因为消耗的吞吐量 （RU/s） 已超过[预配吞吐量](set-throughput.md)。 SDK 会根据指定的[重试策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 检查[门户指标](use-metrics.md)，以确定是否收到了 429 错误。 查看[分区键](partitioning-overview.md#choose-partitionkey)，以确保均匀分配存储和请求量。 

### <a name="slow-query-performance"></a>查询性能较低
[查询指标](sql-api-query-metrics.md)有助于确定查询在哪个位置花费的时间最多。 在查询指标中，可以查看查询在客户端与后端上花费的时间。
* 如果后端查询的返回速度较快，并将大量的时间花费在客户端上，请检查计算机上的负载。 可能的原因是资源不足，SDK 正在等待提供可用的资源来处理响应。
* 如果后端查询速度较慢，请尝试[优化查询](optimize-cost-queries.md)，并查看当前的[索引策略](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[请求速率过大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


