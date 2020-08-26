---
title: 排查 .NET SDK Azure Cosmos DB HTTP 408 或请求超时问题
description: 了解如何诊断和修复 .NET SDK 请求超时异常。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 4e7b1fdbcbf85aa4c64a38deeeb03ede9a0e4b87
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871133"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>诊断和解决 Azure Cosmos DB .NET SDK 请求超时异常
如果 SDK 在超时限制之前无法完成请求，则会出现 HTTP 408 错误。

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>自定义 Azure Cosmos DB .NET SDK 的超时

该 SDK 提供了两个不同选择来用于控制超时，每个都有不同的作用域。

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout` (或 `ConnectionPolicy.RequestTimeout` for SDK v2) 配置允许您设置影响每个单个网络请求的超时。 用户启动的操作可跨多个网络请求 (例如，可能存在限制) 。 此配置将适用于重试的每个网络请求。 此超时不是端到端操作请求超时。

### <a name="cancellationtoken"></a>CancellationToken

SDK 中的所有异步操作都有一个可选的 CancellationToken 参数。 此 [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) 参数在整个操作中跨所有网络请求使用。 在网络请求之间，如果相关令牌已过期，则可能会检查取消令牌并取消操作。 应使用取消标记来定义操作范围的近似预期超时。

> [!NOTE]
> `CancellationToken`参数是一个机制，库将在取消时检查取消操作，而[不会导致无效状态](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)。 当取消中定义的时间已启动时，操作可能无法完全取消。 在该时间启动后，它会在执行此操作时取消。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含请求超时异常的已知原因和解决方案。

### <a name="high-cpu-utilization"></a>CPU 利用率较高
高 CPU 利用率是最常见的情况。 为获得最佳延迟，CPU 使用率应约为40%。 使用10秒作为时间间隔来监视最大 (平均 CPU 使用率) 。 对于跨分区查询，CPU 峰值更常见，在这种情况下，单个查询可能会执行多个连接。

#### <a name="solution"></a>解决方案：
使用 SDK 的客户端应用程序应增加或缩减。

### <a name="socket-or-port-availability-might-be-low"></a>插座或端口可用性可能会很低
使用 .NET SDK 的客户端在 Azure 中运行时，可能会遇到 Azure SNAT (PAT) 端口耗尽问题。

#### <a name="solution-1"></a>解决方案 1：
如果在 Azure Vm 上运行，请遵循 [SNAT 端口消耗指南](troubleshoot-dot-net-sdk.md#snat)。

#### <a name="solution-2"></a>解决方案 2：
如果在 Azure App Service 上运行，请遵循 [连接错误疑难解答指南](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) ，并 [使用应用服务诊断](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)。

#### <a name="solution-3"></a>解决方案3：
如果在 Azure Functions 上运行，请验证是否遵循了 [Azure Functions 建议](../azure-functions/manage-connections.md#static-clients) ，以便为所有涉及的 (服务（包括 Azure Cosmos DB) ）维护单独或静态客户端。 根据 Function App 承载的类型和大小检查 [服务限制](../azure-functions/functions-scale.md#service-limits) 。

#### <a name="solution-4"></a>解决方案4：
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。 否则，会遇到连接问题。

### <a name="create-multiple-client-instances"></a>创建多个客户端实例
创建多个客户端实例可能会导致连接争用和超时问题。

#### <a name="solution"></a>解决方案：
遵循[性能提示](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)，并在整个过程中使用单个 DocumentClient 实例。

### <a name="hot-partition-key"></a>热分区键
Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 当存在热分区时，物理分区上的一个或多个逻辑分区键会消耗所有物理分区的每秒请求单位数)  (RU/秒）。 同时，其他物理分区上的 RU/s 将无法使用。 作为症状，所用的 RU/秒总数将小于数据库或容器上的总体预配 RU/s，但仍会在针对热逻辑分区键的请求上看到限制 (429s) 。 使用 [规范化的 RU 消耗指标](monitor-normalized-request-units.md) 来查看工作负荷是否遇到热分区。 

#### <a name="solution"></a>解决方案：
选择均匀分配请求量和存储的适当分区键。 了解如何[更改分区键](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)。

### <a name="high-degree-of-concurrency"></a>高并发度
应用程序正在执行较高级别的并发，这可能会导致通道上出现争用。

#### <a name="solution"></a>解决方案：
使用 SDK 的客户端应用程序应增加或缩减。

### <a name="large-requests-or-responses"></a>大型请求或响应
较大的请求或响应可能导致通道上出现队头阻塞并加剧资源争用（甚至在并发度相对较低的情况下）。

#### <a name="solution"></a>解决方案：
使用 SDK 的客户端应用程序应增加或缩减。

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>失败率在 Azure Cosmos DB SLA 内
应用程序应该能够处理暂时性故障，并在必要时重试。 不会重试任何408异常，因为在创建路径上，不可能知道服务是否创建了该项。 再次发送相同的项进行创建将导致冲突异常。 用户应用程序业务逻辑可能有处理冲突的自定义逻辑，这会中断现有项的不明确之处与创建重试冲突。

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>失败率违反 Azure Cosmos DB SLA
联系 [Azure 支持部门](https://aka.ms/azure-support)。

## <a name="next-steps"></a>后续步骤
* [诊断并解决](troubleshoot-dot-net-sdk.md) 使用 AZURE COSMOS DB .net SDK 时遇到的问题。
* 了解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的性能准则。
