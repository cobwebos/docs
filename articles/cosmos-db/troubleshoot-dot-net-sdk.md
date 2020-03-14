---
title: 使用 Azure Cosmos DB .NET SDK 时诊断和解决问题
description: 使用客户端日志记录等功能和其他第三方工具来确定、诊断和排查使用 .NET SDK 时遇到的 Azure Cosmos DB 问题。
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137948"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>使用 Azure Cosmos DB .NET SDK 时诊断和解决问题
本文介绍将[.NET SDK](sql-api-sdk-dotnet.md)与 AZURE COSMOS DB SQL API 帐户一起使用时的常见问题、解决方法、诊断步骤和工具。
.NET SDK 提供了用于访问 Azure Cosmos DB SQL API 的客户端逻辑表示形式。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

## <a name="checklist-for-troubleshooting-issues"></a>解决问题的清单：
在将应用程序移至生产环境之前，请考虑以下核对清单。 使用清单会阻止你可能会看到的几个常见问题。 在出现问题时，还可以快速诊断：

*    使用最新的[SDK](sql-api-sdk-dotnet-standard.md)。 预览版 Sdk 不应用于生产环境。 这将阻止遇到已修复的已知问题。
*    查看[性能提示](performance-tips.md)并按照建议的做法进行操作。 这将有助于防止缩放、延迟和其他性能问题。
*    启用 SDK 日志记录，以帮助你解决问题。 启用日志记录可能会影响性能，因此最好仅在解决问题时才启用日志记录。 可以启用以下日志：
    *    使用 Azure 门户[记录指标](monitor-accounts.md)。 门户指标显示 Azure Cosmos DB 遥测，这有助于确定此问题是与 Azure Cosmos DB 相对应还是来自客户端。
    *    从点操作响应中，将 V2 SDK 中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)或 V3 sdk 中的[诊断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)记录到日志中。
    *    记录所有查询响应中的[SQL 查询度量值](sql-api-query-metrics.md) 
    *    遵循[SDK 日志记录]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的设置

请查看本文中的[常见问题和解决方法](#common-issues-workarounds)部分。

查看受主动监视的[GitHub 问题部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 检查是否已提交包含解决方法的任何类似问题。 如果找不到解决方案，请发布 GitHub 问题。 你可以为紧急问题开启支持勾选标记。


## <a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="general-suggestions"></a>常规建议
* 尽可能在与 Azure Cosmos DB 帐户相同的 Azure 区域中运行应用。 
* 由于你的客户端计算机上缺少资源，你可能会遇到连接/可用性问题。 建议在运行 Azure Cosmos DB 客户端的节点上监视 CPU 使用率，并在高负载情况下向上或向外扩展。

### <a name="check-the-portal-metrics"></a>检查门户指标
检查[门户指标](monitor-accounts.md)可帮助确定这是否是客户端问题，或者服务是否出现问题。 例如，如果指标包含较高速率-受限请求（HTTP 状态代码429），这意味着请求受到限制，则检查 "[请求速率太大]" 部分。 

### <a name="request-timeouts"></a>请求超时
使用 Direct/TCP 时通常会发生 RequestTimeout，但可以在网关模式下进行。 这些错误是常见的已知原因，并提供有关如何解决此问题的建议。

* CPU 利用率较高，这会导致延迟和/或请求超时。 客户可以纵向扩展主机以向其分配更多资源，或者可以在多台计算机之间分布负载。
* 插座/端口可用性可能较低。 在 Azure 中运行时，使用 .NET SDK 的客户端可以访问 Azure SNAT （PAT）端口耗尽。 若要减少出现此问题的机率，请使用最新版本的 .NET SDK。 这是为什么建议始终运行最新 SDK 版本的示例。
* 创建多个 DocumentClient 实例可能会导致连接争用和超时问题。 遵循[性能提示](performance-tips.md)，在整个过程中使用单个 DocumentClient 实例。
* 由于用户的集合预配不足、后端限制请求和客户端在内部重试，有时会看到提升的延迟或请求超时。 检查[门户指标](monitor-accounts.md)。
* Azure Cosmos DB 在物理分区之间平均分配总体预配吞吐量。 检查门户指标，查看工作负荷是否遇到热[分区键](partition-data.md)。 这将导致聚合消耗的吞吐量（RU/s）看起来像预配的 ru 下，但使用的单个分区吞吐量（RU/s）将超出预配的吞吐量。 
* 另外，2.0 SDK 将通道语义添加到直接/TCP 连接。 同时为多个请求使用一个 TCP 连接。 在特定情况下，这可能会导致两个问题：
    * 高程度的并发可能导致通道上的争用。
    * 即使并发度相对较低，大型请求或响应也可能导致通道上的恶化，并导致争用。
    * 如果事例属于这两个类别中的任何一种（如果怀疑 CPU 使用率较高，则可能是这样的缓解措施）：
        * 尝试向上/向外扩展应用程序。
        * 此外，可以通过[跟踪侦听器](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)捕获 SDK 日志以获取更多详细信息。

### <a name="high-network-latency"></a>高网络延迟
可以使用 V2 SDK 中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)或 V3 sdk 中的[诊断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)来识别高网络延迟。

如果没有[超时](#request-timeouts)，且诊断显示单个请求，其中的高延迟可用于 `ResponseTime` 和 `RequestStartTime`之间的差异，如以下示例中所示（> 300 毫秒）：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

此延迟可能有多种原因：

* 您的应用程序未在与 Azure Cosmos DB 帐户相同的区域中运行。
* 你的[PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations)或[ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion)配置不正确，并且正在尝试连接到你的应用程序当前运行所在的其他区域。
* 由于流量大，网络接口上可能存在瓶颈。 如果应用程序在 Azure 虚拟机上运行，则有可能的解决方法：
    * 请考虑使用[启用了加速网络的虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * [在现有虚拟机上启用加速网络](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 请考虑使用[更高端的虚拟机](../virtual-machines/windows/sizes.md)。

### <a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果在[没有公共 IP 地址的 Azure 虚拟机](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)上部署应用，则默认情况下， [Azure SNAT 端口](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)会与 VM 外部的任何终结点建立连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)的限制。 这种情况可能会导致连接限制、连接关闭或上面提到的[请求超时](#request-timeouts)。

 仅当 VM 的专用 IP 地址连接到公共 IP 地址时，才使用 Azure SNAT 端口。 可以通过两种方法来避免 Azure SNAT 限制（前提是已在整个应用程序中使用单个客户端实例）：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](../virtual-network/virtual-networks-acl.md) 将子网添加到防火墙。
* 向[AZURE VM 分配公共 IP](../load-balancer/load-balancer-outbound-connections.md#assignilpip)。

### <a name="http-proxy"></a>HTTP 代理
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

### <a name="request-rate-too-large"></a>请求速率太大
"请求速率过大" 或错误代码429指示正在阻止你的请求，因为消耗的吞吐量（RU/s）超过了[预配的吞吐量](set-throughput.md)。 SDK 会根据指定的[重试策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自动重试请求。 如果经常出现此问题，请考虑增加集合的吞吐量。 检查[门户的指标](use-metrics.md)，查看是否收到429错误。 检查[分区键](partitioning-overview.md#choose-partitionkey)以确保它会导致存储和请求卷的分配。 

### <a name="slow-query-performance"></a>查询性能缓慢
[查询指标](sql-api-query-metrics.md)可帮助确定查询在大多数时间的最大时间。 在查询指标中，你可以查看在后端与客户端上的数据量。
* 如果后端查询快速返回，并且在客户端上花费较长时间，请检查计算机上的负载。 可能是资源不足，SDK 正在等待资源可用于处理响应的情况。
* 如果后端查询速度较慢，请尝试[优化查询](optimize-cost-queries.md)，并查看当前的[索引策略](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[请求速率太大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


