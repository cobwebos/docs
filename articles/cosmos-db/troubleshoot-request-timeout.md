---
title: 排查 Azure Cosmos DB 服务请求超时异常
description: 如何诊断和修复 Cosmos DB 服务请求超时异常
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294016"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>诊断并解决 Azure Cosmos DB 请求超时
Azure Cosmos DB 返回了 HTTP 408 请求超时

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含请求超时异常的已知原因和解决方案。

### <a name="1-check-the-sla"></a>1. 检查 SLA
客户应检查[Azure Cosmos DB 监视](monitor-cosmos-db.md)，以检查 number 408 异常是否违反了 Cosmos DB SLA。

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>解决方案1：它不违反 Cosmos DB SLA
应用程序应处理此方案，并在发生这些暂时性故障时重试。

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>解决方案2：违反 Cosmos DB SLA
联系 Azure 支持人员：https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. 热分区键
Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 如果存在热分区，则物理分区上的一个或多个逻辑分区键会消耗全部物理分区的 RU/s，而其他物理分区上的 RU/s 则不会使用。 作为症状，所用的 RU/秒总数将小于数据库或容器上的总体预配 RU/s，但会在针对热逻辑分区键的请求上看到限制（429s）。 使用[规范化的 RU 消耗指标](monitor-normalized-request-units.md)来查看工作负荷是否遇到热分区。 

#### <a name="solution"></a>解决方案：
选择均匀分配请求量和存储的适当的分区键。 了解如何[更改分区键](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)。

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则