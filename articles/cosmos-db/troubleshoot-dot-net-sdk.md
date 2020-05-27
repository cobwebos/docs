---
title: 诊断和排查在使用 Azure Cosmos DB .NET SDK 时出现的问题
description: 通过客户端日志记录等功能及其他第三方工具来识别、诊断和排查在使用 .NET SDK 时出现的 Azure Cosmos DB 问题。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e389df7cfe0e228030d2d0f730fc5e671ad4c052
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927626"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>诊断和排查在使用 Azure Cosmos DB .NET SDK 时出现的问题

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

本文介绍将 [.NET SDK](sql-api-sdk-dotnet.md) 与 Azure Cosmos DB SQL API 帐户配合使用时的常见问题、解决方法、诊断步骤和工具。
.NET SDK 提供了客户端逻辑表示，以用于访问 Azure Cosmos DB SQL API。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

## <a name="checklist-for-troubleshooting-issues"></a>问题排查清单
在将应用程序转移到生产环境之前，请考虑根据以下清单进行检查。 使用该清单有助于防止出现多个常见问题。 出现问题时可以快速诊断：

*    使用最新的 [SDK](sql-api-sdk-dotnet-standard.md)。 不要将预览版 SDK 用于生产。 这样就可以避免遇到已更正的已知问题。
*    查看[性能提示](performance-tips.md)并按照建议的做法进行操作。 这有助于避免缩放、延迟和其他性能问题。
*    启用 SDK 日志记录以帮助排查问题。 启用日志记录可能会影响性能，因此，最好是只在排查问题时才启用日志记录。 可以启用以下日志：
    *    使用 Azure 门户[记录指标](monitor-accounts.md)。 门户指标显示 Azure Cosmos DB 遥测数据，这有助于确定问题是否与 Azure Cosmos DB 相关，或者是否由客户端造成。
    *    记录点操作响应中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)（在 V2 SDK 中）或[诊断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)（在 V3 SDK 中）。
    *    记录所有查询响应中的 [SQL 查询指标](sql-api-query-metrics.md) 
    *    遵循针对 [SDK 日志记录]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的设置步骤

请查看本文中的[常见问题和解决方法](#common-issues-workarounds)部分。

查看我们积极关注的 [GitHub 问题部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 检查是否已提交包含解决方法的任何类似问题。 如果找不到解决方法，请提出 GitHub 问题。 对于紧急问题，可以开具支持票证。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="general-suggestions"></a>常规建议
* 尽可能在 Azure Cosmos DB 帐户所在的同一个 Azure 区域中运行应用。 
* 由于客户端计算机上的资源不足，你可能会遇到连接/可用性问题。 我们建议监视运行 Azure Cosmos DB 客户端的节点上的 CPU 利用率，如果这些节点的负载较大，请纵向/横向扩展节点。

### <a name="check-the-portal-metrics"></a>检查门户指标
检查[门户指标](monitor-accounts.md)有助于确定问题是否与客户端相关，或者服务是否有问题。 例如，如果指标中包含较高比率的速率受限请求（HTTP 状态代码 429，表示请求受到限制），请查看[请求速率过大]部分。 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>请求超时
使用直接/TCP 连接时往往会发生 RequestTimeout，但在网关模式下也可能会发生。 下面是常见的已知原因，以及有关如何解决问题的建议。

* CPU 利用率较高，导致延迟和/或请求超时。 客户可以纵向扩展主机以便为其提供更多的资源，或者可将负载分散到更多的计算机。
* 插槽/端口可用性可能较低。 使用 .NET SDK 的客户端在 Azure 中运行时，可能会遇到 Azure SNAT (PAT) 端口耗尽问题。 为了减少出现此问题的可能性，请使用 .NET SDK 最新版本 2.x 或 3.x。 这也是我们建议始终运行最新 SDK 版本的原因之一。
* 创建多个 DocumentClient 实例可能会导致连接争用和超时问题。 遵循[性能提示](performance-tips.md)，并在整个过程中使用单个 DocumentClient 实例。
* 用户有时会看到延迟或请求超时增大，原因是集合的预配不足，后端限制了请求，并且客户端在内部重试。 检查[门户指标](monitor-accounts.md)。
* Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 请检查门户指标，以确定工作负荷是否遇到了热[分区键](partition-data.md)。 热分区键会导致聚合的已消耗吞吐量 (RU/s) 看上去低于预配的 RU，但单个分区消耗的吞吐量 (RU/s) 会超过预配的吞吐量。 
* 此外，2.0 SDK 将通道语义添加到了直接/TCP 连接。 同时为多个请求使用一个 TCP 连接。 在特定的情况下，这可能会导致两个问题：
    * 高并发度可能导致通道中出现争用。
    * 较大的请求或响应可能导致通道上出现队头阻塞并加剧资源争用（甚至在并发度相对较低的情况下）。
    * 如果遇到上述两种情况之一（或者怀疑 CPU 利用率较高），可能的缓解措施如下：
        * 尝试纵向/横向扩展应用程序。
        * 此外，可以通过[跟踪侦听器](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)捕获 SDK 日志，以获取更多详细信息。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高网络延迟
可以使用 V2 SDK 中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)或 V3 SDK 中的[诊断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)来识别高网络延迟。

如果未发生[超时](#request-timeouts)，并且诊断根据 `ResponseTime` 和 `RequestStartTime`之差显示单个请求的延迟明显较高（在本示例中超过 300 毫秒），如下所示：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

这种延迟的可能原因有多种：

* 应用程序不是在 Azure Cosmos DB 帐户所在的同一区域中运行。
* [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 或 [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 配置不正确，尝试连接到的区域不是当前正在运行应用程序的区域。
* 由于流量较大，网络接口可能出现了瓶颈。 如果应用程序在 Azure 虚拟机上运行，则有两种可能的解决方法：
    * 考虑使用[启用了加速网络的虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * 启用[现有虚拟机上的加速网络](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 考虑使用[更高端的虚拟机](../virtual-machines/windows/sizes.md)。

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在[没有公共 IP 地址的 Azure 虚拟机](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)上，则默认情况下，[Azure SNAT 端口](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)将与 VM 外部的任何终结点建立连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)的限制。 这种情况可能会导致连接限制、连接关闭或上述[请求超时](#request-timeouts)。

 仅当 VM 具有专用 IP 地址且连接到公共 IP 地址时，才会使用 Azure SNAT 端口。 有两种解决方法可以避免 Azure SNAT 限制（前提是已在整个应用程序中使用单个客户端实例）：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](../virtual-network/virtual-networks-acl.md) 将子网添加到防火墙。
* [将公共 IP 分配给 Azure VM](../load-balancer/load-balancer-outbound-connections.md#assignilpip)。

### <a name="http-proxy"></a>HTTP 代理
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>请求速率过大
“请求速率过大”或错误代码 429 表示请求正受到限制，因为消耗的吞吐量 (RU/s) 已超过[预配的吞吐量](set-throughput.md)。 SDK 会根据指定的[重试策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 检查[门户指标](use-metrics.md)，以确定是否收到了 429 错误。 查看[分区键](partitioning-overview.md#choose-partitionkey)，以确保均匀分配存储和请求量。 

### <a name="slow-query-performance"></a>查询性能较低
[查询指标](sql-api-query-metrics.md)有助于确定查询在何处花费的时间最多。 在查询指标中，可以查看查询在客户端与后端上花费的时间。
* 如果后端查询的返回速度较快，并将大量的时间花费在客户端上，请检查计算机上的负载。 可能的原因是资源不足，SDK 正在等待资源可用于处理响应。
* 如果后端查询速度较慢，请尝试[优化查询](optimize-cost-queries.md)，并查看当前的[索引策略](index-overview.md) 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401：HTTP 请求中的 MAC 签名与计算出的签名不同
如果收到以下 401 错误消息：“HTTP 请求中的 MAC 签名与计算出的签名不同。” 此错误可能是由以下情况造成的。

1. 密钥已轮换，且未遵循[最佳做法](secure-access-to-data.md#key-rotation)。 这往往是问题所在。 完成 Cosmos DB 帐户密钥轮换可能需要几秒到几天的时间，具体取决于 Cosmos DB 帐户大小。
   1. 密钥轮换之后不久就会出现 401 MAC 签名问题，但无需进行任何更改，它最终会停止。 
2. 应用程序上的密钥配置错误，导致密钥与帐户不匹配。
   1. 401 MAC 签名问题持续出现并在所有调用中发生
3. 创建容器时出现争用状况。 在完成容器创建之前，某个应用程序实例正在尝试访问容器。 出现此状况的最常见情况是，应用程序正在运行，就删除了容器，并在应用程序正在运行时重新创建了同名的容器。 SDK 将尝试使用新容器，但由于容器创建仍在进行，因此无法获得密钥。
   1. 创建容器后不久就会出现 401 MAC 签名问题，且只会在创建完容器之后才出现。
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>HTTP 错误 400。 请求标头的大小过长。
 标头大小变得很大，即将超过允许的最大大小。 始终建议使用最新的 SDK。 请确保至少使用版本 [3.x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) 或 [2.x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)，这些版本会在异常消息中添加标头大小跟踪。

原因：
 1. 会话标记过大。 会话标记随着容器中分区数量的增加而增大。
 2. 继续标记已变得很大。 不同的查询具有不同的继续标记大小。
 3. 此问题是由会话标记和继续标记共同造成的。

解决方案：
   1. 遵循[性能提示](performance-tips.md)，并将应用程序转换为“直接 + TCP”连接模式。 “直接 + TCP”模式不像 HTTP 模式那样存在标头大小限制，可以避免此问题。
   2. 如果会话标记是问题所在，则暂时的缓解措施是重启应用程序。 重启应用程序实例会重置会话标记。 如果在重启后不再出现异常，则可以确认会话标记是问题所在。 会话标记最终仍会恢复到导致异常的大小。
   3. 如果无法将应用程序转换为“直接 + TCP”模式，而会话标记是问题所在，可以通过更改客户端[一致性级别](consistency-levels.md)进行缓解。 会话标记仅用于实现会话一致性（Cosmos DB 的默认一致性级别）。 任何其他一致性级别都不使用会话标记。 
   4. 如果无法将应用程序转换为“直接 + TCP”模式并且继续标记是问题所在，请尝试设置 ResponseContinuationTokenLimitInKb 选项。 可在 v2 的 FeedOptions 或 v3 的 QueryRequestOptions 中找到该选项。

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[请求速率过大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
