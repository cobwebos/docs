---
title: 排查 Azure Cosmos DB 请求速率为大异常的问题
description: 如何诊断和修复请求速率为大异常
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294015"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>诊断和解决 Cosmos DB 太多请求
"请求速率过大" 或错误代码429指示正在阻止你的请求。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含了太多请求的已知原因和解决方法。

### <a name="1-check-the-metrics"></a>1. 检查指标
客户应检查[Azure Cosmos DB 监视](monitor-cosmos-db.md)，以检查429的数量是否有异常。

## <a name="cause"></a>原因：
已使用的吞吐量（RU/s）超过了[预配的吞吐量](set-throughput.md)。 SDK 会根据指定的重试策略自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 检查门户指标，以确定是否收到了 429 错误。 检查分区键以确保它会导致[存储和请求卷的分配](partition-data.md)。

## <a name="solution"></a>解决方案：
1. 使用[门户或 SDK](set-throughput.md)增加预配的吞吐量。
2. 将数据库或容器切换为[自动缩放](provision-throughput-autoscale.md)。

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则