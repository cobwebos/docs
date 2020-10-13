---
title: 在 multiregional 环境中诊断并解决 Azure Cosmos Sdk 的可用性问题
description: 了解有关在多区域环境中操作时的 Azure Cosmos SDK 可用性行为的全部信息。
author: ealsur
ms.service: cosmos-db
ms.date: 10/05/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 400795d20b6e7ad919f5cbbfa6078987bb65297e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743958"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>在 multiregional 环境中诊断并解决 Azure Cosmos Sdk 的可用性问题

本文介绍 Azure Cosmos Sdk 的最新版本在出现到特定区域的连接问题或发生区域故障转移时的行为。

所有 Azure Cosmos Sdk 都可选择自定义区域首选项。 以下属性用于不同的 Sdk：

* .NET V2 SDK 中的 [ConnectionPolicy PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 属性。
* .NET V3 SDK 中的 [CosmosClientOptions ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 或 [CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) 属性。
* Java V4 SDK 中的 [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) 方法。
* Python SDK 中的 [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 参数。
* [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations)参数。

设置区域首选项时，客户端将连接到下表中所述的区域：

|帐户类型 |读取 |写入 |
|------------------------|--|--|
| 单个写入区域 | 首选区域 | 主要区域  |
| 多个写入区域 | 首选区域 | 首选区域  |

如果未设置首选区域：

|帐户类型 |读取 |写入 |
|------------------------|--|--|
| 单个写入区域 | 主要区域 | 主要区域 |
| 多个写入区域 | 主要区域  | 主要区域  |

> [!NOTE]
> 主要区域指的是[Azure Cosmos 帐户区域列表](distribute-data-globally.md)中的第一个区域

发生以下任何情况时，使用 Azure Cosmos SDK 的客户端将公开日志，并在 **操作诊断信息**中包含重试信息：

* .NET V2 SDK 中响应的 *RequestDiagnosticsString* 属性。
* .NET V3 SDK 中响应和异常的 *诊断* 属性。
* GetDiagnostics 对 Java V4 SDK 中的响应和异常 * ( # B1 * 方法。

在按优先顺序确定下一个区域时，SDK 客户端将使用帐户区域列表，并确定首选区域的优先级 (如果有任何) 。

有关这些事件中的 SLA 保证的综合性详细信息，请参阅 [适用于可用性的 sla](high-availability.md#slas-for-availability)。

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>从帐户中删除区域

当你从 Azure Cosmos 帐户中删除某个区域时，主动使用该帐户的任何 SDK 客户端都将通过后端响应代码检测区域删除。 然后，客户端将区域终结点标记为不可用。 客户端会重试当前操作，所有将来的操作将按优先顺序永久路由到下一个区域。

## <a name="adding-a-region-to-an-account"></a>将区域添加到帐户

Azure Cosmos SDK 客户端每5分钟读取一次帐户配置，并刷新其识别的区域。

如果删除某个区域，然后将其重新添加回帐户，则在 SDK 配置中，如果添加的区域的区域首选项顺序高于当前连接的区域，SDK 将切换回使用此区域。 检测到添加的区域后，所有将来的请求都将定向到该区域。

如果将客户端配置为最好连接到 Azure Cosmos 帐户没有的区域，则将忽略首选区域。 如果以后添加该区域，客户端将检测到该区域，并将其永久切换到该区域。

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>在单个写入区域帐户中故障转移写入区域

如果启动当前写入区域的故障转移，则下一个写入请求将会失败，并会出现已知的后端响应。 检测到此响应时，客户端将查询帐户以了解新的写入区域，并继续重试当前操作，并将所有将来的写入操作永久路由到新区域。

## <a name="regional-outage"></a>区域性服务中断

如果该帐户是单一写入区域，并且在执行写入操作期间发生区域中断，则此行为类似于 [手动故障转移](#manual-failover-single-region)。 对于读取请求或多个写入区域帐户，行为类似于 [删除区域](#remove-region)。

## <a name="session-consistency-guarantees"></a>会话一致性保证

当使用 [会话一致性](consistency-levels.md#guarantees-associated-with-consistency-levels)时，客户端需要确保它可以读取自己的写入。 在 "读取区域" 首选项不同于写入区域的 "单一写入区域帐户" 中，可能会出现这样的情况：用户发出了写入操作，并在从本地区域读取数据时，本地区域尚未收到 (轻型约束) 速度的数据复制。 在这种情况下，SDK 会检测读取操作的特定故障，并在中心区域重试读取操作，以确保会话一致性。

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 协议上的暂时性连接问题

在将 Azure Cosmos SDK 客户端配置为使用 TCP 协议的情况下，对于给定请求，可能存在网络条件暂时影响与特定终结点的通信的情况。 这些临时网络条件可能会显示为 TCP 超时。 在几秒钟内，客户端将在同一终结点上以本地方式重试请求。

如果用户已配置了具有多个区域的首选区域列表，而 Azure Cosmos 帐户是多个写入区域或单个写入区域，并且操作是一个读取请求，则客户端将从首选项列表中重试该单一操作。

## <a name="next-steps"></a>后续步骤

* 查看 [可用性 sla](high-availability.md#slas-for-availability)。
* 使用最新的 [.NET SDK](sql-api-sdk-dotnet-standard.md)
* 使用最新的 [JAVA SDK](sql-api-sdk-java-v4.md)
* 使用最新的 [PYTHON SDK](sql-api-sdk-python.md)
* 使用最新的 [NODE SDK](sql-api-sdk-node.md)
