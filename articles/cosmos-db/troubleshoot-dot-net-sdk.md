---
title: 诊断和排查在使用 Azure Cosmos DB .NET SDK 时出现的问题
description: 通过客户端日志记录等功能及其他第三方工具来识别、诊断和排查在使用 .NET SDK 时出现的 Azure Cosmos DB 问题。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 09/12/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 581c8fcad62c40555a90b7455a260259f3a09212
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802407"
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
检查[门户指标](monitor-accounts.md)有助于确定问题是否与客户端相关，或者服务是否有问题。 例如，如果指标中包含较高比率的速率受限请求（HTTP 状态代码 429，表示请求受到限制），请查看[请求速率过大](troubleshoot-request-rate-too-large.md)部分。 

## <a name="common-error-status-codes"></a>常见错误状态代码 <a id="error-codes"></a>

| 状态代码 | 说明 | 
|----------|-------------|
| 400 | 错误请求（取决于错误消息）| 
| 401 | [未授权](troubleshoot-unauthorized.md) | 
| 404 | [找不到资源](troubleshoot-not-found.md) |
| 408 | [请求已超时](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | 冲突失败是指为写入操作中的资源提供的 ID 已被现有资源使用。 对资源使用另一个 ID 可解决此问题，因为 ID 在具有相同分区键值的所有文档中必须唯一。 |
| 410 | 消失异常（不应违反 SLA 的瞬间失败） |
| 412 | 前提条件失败是操作指定的 eTag 与服务器上提供的版本不同。 这是乐观并发错误。 在读取资源的最新版本并更新请求中的 eTag 后重试该请求。
| 413 | [请求实体太大](concepts-limits.md#per-item-limits) |
| 429 | [请求过多](troubleshoot-request-rate-too-large.md) |
| 449 | 仅在进行写入操作时才发生的暂时性错误，可安全重试 |
| 500 | 操作由于意外服务错误而失败。 联系支持人员。 请参阅“申报 [Azure 支持问题](https://aka.ms/azure-support)”。 |
| 503 | [服务不可用](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在[没有公共 IP 地址的 Azure 虚拟机](../load-balancer/load-balancer-outbound-connections.md)上，则默认情况下，[Azure SNAT 端口](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)用于建立与 VM 外部任何终结点的连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)的限制。 这种情况可能会导致连接限制、连接关闭或上述[请求超时](troubleshoot-dot-net-sdk-request-timeout.md)。

 仅当 VM 具有专用 IP 地址且连接到公共 IP 地址时，才会使用 Azure SNAT 端口。 有两种解决方法可以避免 Azure SNAT 限制（前提是已在整个应用程序中使用单个客户端实例）：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](../virtual-network/virtual-networks-acl.md) 将子网添加到防火墙。
* [将公共 IP 分配给 Azure VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip)。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高网络延迟
可以使用 V2 SDK 中的 [diagnosticsstring](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet&preserve-view=true) 或 V3 SDK 中的 [diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) 来识别网络延迟过高的情况。

如果未发生[超时](troubleshoot-dot-net-sdk-request-timeout.md)，并且诊断根据 `ResponseTime` 和 `RequestStartTime`之差显示单个请求的延迟明显较高（在本示例中超过 300 毫秒），如下所示：

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

### <a name="common-query-issues"></a>常见查询问题

[查询指标](sql-api-query-metrics.md)有助于确定查询在何处花费的时间最多。 在查询指标中，可以查看查询在客户端与后端上花费的时间。 详细了解如何 [排查查询性能问题](troubleshoot-query-performance.md)。

* 如果后端查询的返回速度较快，并将大量的时间花费在客户端上，请检查计算机上的负载。 可能的原因是资源不足，SDK 正在等待资源可用于处理响应。
* 如果后端查询速度较慢，请尝试 [优化查询](troubleshoot-query-performance.md) ，并查看当前的 [索引策略](index-overview.md)

    > [!NOTE]
    > 为获得提升的性能，建议使用 Windows 64 位主机处理。 SQL SDK 包含一个本机 ServiceInterop.dll，用于在本地分析和优化查询。 仅 Windows x64 平台支持 ServiceInterop.dll。 对于 ServiceInterop.dll 在其中不可用的 Linux 平台及其他不受支持的平台，将对网关进行额外的网络调用以获取优化的查询。

如果遇到以下错误： `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` 并且使用的是 windows，则应升级到最新的 windows 版本。

## <a name="next-steps"></a>后续步骤

* 了解 [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET V2](performance-tips.md) 的性能准则
* 了解[基于 Reactor 的 Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
