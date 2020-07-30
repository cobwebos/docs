---
title: 排查 .NET SDK Azure Cosmos DB HTTP 408 或请求超时问题
description: 如何诊断和修复 .NET SDK 请求超时异常
author: j82w
ms.service: cosmos-db
ms.date: 07/29/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 3d6fed539581b2d1add87ade92e34bcf2e1913e8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417601"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>诊断和解决 Azure Cosmos DB .NET SDK 请求超时
如果 SDK 在超时限制之前无法完成请求，则会出现 HTTP 408 错误。

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>自定义 Azure Cosmos .NET SDK 的超时

SDK 具有两个不同的方法来控制超时，每个都有不同的作用域。

### <a name="requesttimeout"></a>RequestTimeout

使用 `CosmosClientOptions.RequestTimeout` （或 `ConnectionPolicy.RequestTimeout` SDK V2）配置，可以设置影响每个单个网络请求的超时。  用户启动的操作可以跨多个网络请求（例如，可能会受到限制），此配置将适用于重试时的每个网络请求。 这不是端到端操作请求超时。

### <a name="cancellationtoken"></a>CancellationToken

SDK 中的所有异步操作都有一个可选的 CancellationToken 参数。 此[CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling)在整个操作中跨所有网络请求使用。 在网络请求之间，如果相关令牌已过期，则可能会检查 CancellationToken 并取消操作。 应使用 CancellationToken 来定义操作范围的近似预期超时。

> [!NOTE]
> CancellationToken 是一种机制，库将在取消时检查取消操作，而[不会导致无效状态](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)。 当取消中定义的时间已启动时，该操作可能不会完全取消，而是在该时间启动后，它会在执行此操作时取消。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含请求超时异常的已知原因和解决方案。

### <a name="1-high-cpu-utilization-most-common-case"></a>1. CPU 使用率高（最常见情况）
为实现最佳延迟，建议 CPU 使用率大约为40%。 建议使用10秒作为时间间隔来监视最大值（而不是平均 CPU 使用率）。 对于跨分区查询，CPU 峰值更常见，在这种情况下，单个查询可能会执行多个连接。

#### <a name="solution"></a>解决方案：
使用 SDK 的客户端应用程序应向上扩展/向外扩展。

### <a name="2-socket--port-availability-might-be-low"></a>2. 插座/端口可用性可能较低
使用 .NET SDK 的客户端在 Azure 中运行时，可能会遇到 Azure SNAT (PAT) 端口耗尽问题。

#### <a name="solution-1"></a>解决方案 1：
遵循[SNAT 端口消耗指南](troubleshoot-dot-net-sdk.md#snat)。

#### <a name="solution-2"></a>解决方案 2：
如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

### <a name="3-creating-multiple-client-instances"></a>3. 创建多个客户端实例
创建多个客户端实例可能会导致连接争用和超时问题。

#### <a name="solution"></a>解决方案：
遵循[性能提示](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)，在整个过程中使用单个 CosmosClient 实例。

### <a name="4-hot-partition-key"></a>4. 热分区键
Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 如果存在热分区，则物理分区上的一个或多个逻辑分区键会消耗全部物理分区的 RU/s，而其他物理分区上的 RU/s 则不会使用。 作为症状，所用的 RU/秒总数将小于数据库或容器上的总体预配 RU/s，但会在针对热逻辑分区键的请求上看到限制（429s）。 使用[规范化的 RU 消耗指标](monitor-normalized-request-units.md)来查看工作负荷是否遇到热分区。 

#### <a name="solution"></a>解决方案：
选择均匀分配请求量和存储的适当的分区键。 了解如何[更改分区键](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)。

### <a name="5-high-degree-of-concurrency"></a>5. 高并发度
应用程序正在执行高级别的并发，这可能会导致通道上出现争用

#### <a name="solution"></a>解决方案：
使用 SDK 的客户端应用程序应向上扩展/向外扩展。

### <a name="6-large-requests-andor-responses"></a>6. 大请求和/或响应
较大的请求或响应可能导致通道上出现队头阻塞并加剧资源争用（甚至在并发度相对较低的情况下）。

#### <a name="solution"></a>解决方案：
使用 SDK 的客户端应用程序应向上扩展/向外扩展。

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. 失败率在 Cosmos DB SLA 内
应用程序应该能够处理暂时性故障，并在必要时重试。 408不重试异常，因为在创建路径上，不能知道服务是否创建了该项，或者它是否未创建。 再次发送相同的项进行创建将导致冲突异常。 用户应用程序业务逻辑可能有用于处理冲突的自定义逻辑，这会中断现有项与 "创建重试" 冲突的歧义。

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. 失败率违反 Cosmos DB SLA
请联系 Azure 支持部门。

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则
