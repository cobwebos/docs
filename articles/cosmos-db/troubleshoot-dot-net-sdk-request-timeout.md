---
title: 排查 .NET SDK 的 Azure Cosmos DB HTTP 408 或请求超时问题
description: 了解如何诊断和修复 .NET SDK 请求超时异常。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 0c760a3a2f6300108c1739f18ef9fa97a40dd833
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021929"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>诊断和排查 Azure Cosmos DB .NET SDK 请求超时异常
如果 SDK 在超时限制发生之前未能完成请求，则会出现 HTTP 408 错误。

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>在 Azure Cosmos DB .NET SDK 中自定义超时

该 SDK 提供了两个不同选择来用于控制超时，每个都有不同的作用域。

### <a name="requesttimeout"></a>RequestTimeout

通过 `CosmosClientOptions.RequestTimeout`（或 SDK v2 的 `ConnectionPolicy.RequestTimeout`）配置，可设置会影响每个单独的网络请求的超时。 用户发起的操作可跨多个网络请求（例如可能会有限制）。 此配置适用于重试时的每个网络请求。 此超时不是端到端操作请求超时。

### <a name="cancellationtoken"></a>CancellationToken

SDK 中的所有异步操作都有一个可选的 CancellationToken 参数。 此 [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) 参数在整个操作过程中对所有网络请求使用。 在网络请求的间隔期间，可能会检查取消令牌；如果相关令牌已过期，则会取消操作。 应使用取消令牌来定义操作范围上的近似预期超时。

> [!NOTE]
> `CancellationToken` 参数是一种机制，其中库将在它[不会导致无效状态](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)时检查取消情况。 当在取消中定义的时间已超过时，操作可能无法完全取消。 而在该时间超过后，它会在可安全执行时取消。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含请求超时异常的已知原因和解决方案。

### <a name="high-cpu-utilization"></a>CPU 利用率较高
最常见的情况是 CPU 利用率较高。 为实现最佳延迟，CPU 利用率应大约为 40%。 将时间间隔设为 10 秒来监视最大 CPU 利用率（而不是平均利用率）。 对于可能会为单个查询执行多个连接的跨分区查询，更常见的情况是出现 CPU 峰值。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。

### <a name="socket-or-port-availability-might-be-low"></a>套接字或端口可用性可能较低
使用 .NET SDK 的客户端在 Azure 中运行时，可能会遇到 Azure SNAT (PAT) 端口耗尽问题。

#### <a name="solution-1"></a>解决方案 1：
如果正在 Azure VM 上运行，请按照 [SNAT 端口耗尽指南](troubleshoot-dot-net-sdk.md#snat)操作。

#### <a name="solution-2"></a>解决方案 2：
如果正在 Azure 应用服务上运行，请按照[连接错误故障排除指南](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)和[使用应用服务诊断](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)操作。

#### <a name="solution-3"></a>解决方案 3：
如果正在 Azure Functions 上运行，请验证是否遵循 [Azure Functions 建议](../azure-functions/manage-connections.md#static-clients)，即是否为所有涉及的服务（包括 Azure Cosmos DB）维护单一实例或静态客户端。 根据函数应用托管的类型和大小查看[服务限制](../azure-functions/functions-scale.md#service-limits)。

#### <a name="solution-4"></a>解决方案 4：
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。 否则将遇到连接问题。

### <a name="create-multiple-client-instances"></a>创建多个客户端实例
创建多个客户端实例可能会导致连接争用和超时问题。

#### <a name="solution"></a>解决方案：
遵循[性能提示](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)，并在整个过程中使用单个 DocumentClient 实例。

### <a name="hot-partition-key"></a>热分区键
Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 存在热分区时，物理分区上的一个或多个逻辑分区键会消耗物理分区的所有请求单位/秒 (RU/s)。 同时，将无法使用其他物理分区上的 RU/s。 故障表现是，所消耗的 RU/s 总数将小于数据库或容器上整体预配的 RU/s，但针对热逻辑分区键仍将显示请求限制 (429s)。 使用 [规范化的 RU 消耗指标](monitor-normalized-request-units.md) 来查看工作负荷是否遇到热分区。 

#### <a name="solution"></a>解决方案：
选择均匀分配请求量和存储的适当分区键。 了解如何[更改分区键](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)。

### <a name="high-degree-of-concurrency"></a>并发度较高
应用程序正在执行高级别的并发，这可能会导致通道上出现争用。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。

### <a name="large-requests-or-responses"></a>大型请求或响应
较大的请求或响应可能导致通道上出现队头阻塞并加剧资源争用（甚至在并发度相对较低的情况下）。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>失败率在 Azure Cosmos DB SLA 范围之内
应用程序应该能够处理暂时性故障，并在必要时重试。 任何 408 异常不会被重试，因为在创建路径时不可能知道服务是否创建了该项。 再次发送相同的项进行创建将导致冲突异常。 用户应用程序业务逻辑可能包含用于处理冲突的自定义逻辑，这会消除现有项的不确定性与来自“创建”重试的冲突。

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>失败率与 Azure Cosmos DB SLA 不符
请联系 [Azure 支持部门](https://aka.ms/azure-support)。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
