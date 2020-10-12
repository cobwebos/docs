---
title: 排除 Azure Cosmos DB 服务请求超时异常的故障
description: 了解如何诊断和修复 Azure Cosmos DB 服务请求超时异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 76a1558534728613dcdedc78b64a0366f2bd643d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871065"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>诊断和排查 Azure Cosmos DB 请求超时异常
Azure Cosmos DB 返回了 HTTP 408 请求超时。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含请求超时异常的已知原因和解决方案。

### <a name="check-the-sla"></a>检查 SLA
检查 [Azure Cosmos DB 监视](monitor-cosmos-db.md)，了解 408 异常的数目是否违反了 Azure Cosmos DB SLA。

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>解决方案 1：它不违反 Azure Cosmos DB SLA
应用程序应处理此方案，并在发生这些暂时性故障时重试。

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>解决方案 2：它确实违反了 Azure Cosmos DB SLA
联系 [Azure 支持](https://aka.ms/azure-support)。
 
### <a name="hot-partition-key"></a>热分区键
Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 存在热分区时，物理分区上的一个或多个逻辑分区键会消耗物理分区的所有请求单位/秒 (RU/s)。 同时，将无法使用其他物理分区上的 RU/s。 症状是，消耗的总 RU/s 将小于数据库或容器中预配的总 RU/s。 你仍然会看到针对热逻辑分区键的请求的限制 (429s)。 使用 [规范化的 RU 消耗指标](monitor-normalized-request-units.md) 来查看工作负荷是否遇到热分区。 

#### <a name="solution"></a>解决方案：
选择均匀分配请求量和存储的适当分区键。 了解如何[更改分区键](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。