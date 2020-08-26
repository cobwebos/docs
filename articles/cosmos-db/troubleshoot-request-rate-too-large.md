---
title: 排查 Azure Cosmos DB 请求速率太大的异常
description: 了解如何诊断和修复请求速率太大的异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871082"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>诊断和解决 Azure Cosmos DB 请求速率太大异常
"请求速率过大" 消息或错误代码429指示你的请求受到限制。

## <a name="troubleshooting-steps"></a>疑难解答步骤
以下部分包含了太多请求的已知原因和解决方法。

### <a name="check-the-metrics"></a>检查度量值
检查 [Azure Cosmos DB 监视](monitor-cosmos-db.md) 以查看429异常的数量。

#### <a name="cause"></a>原因：
 (每秒请求单位数) 超出了 [预配的吞吐量](set-throughput.md)。 SDK 会根据指定的重试策略自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 检查门户的指标，查看是否收到429错误。 查看分区键，以确保[均匀分配存储和请求量](partition-data.md)。

#### <a name="solution"></a>解决方案：
1. 使用[门户或 SDK](set-throughput.md) 增加预配的吞吐量。
1. 将数据库或容器切换为[自动缩放](provision-throughput-autoscale.md)。

## <a name="next-steps"></a>后续步骤
* [诊断并解决](troubleshoot-dot-net-sdk.md) 使用 AZURE COSMOS DB .net SDK 时遇到的问题。
* 了解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的性能准则。