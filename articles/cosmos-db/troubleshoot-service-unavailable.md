---
title: 排查“Azure Cosmos DB 服务不可用”异常
description: 了解如何诊断和修复“Azure Cosmos DB 服务不可用”异常。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870861"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>诊断和排查“Azure Cosmos DB 服务不可用”异常
此 SDK 无法连接到 Azure Cosmos DB。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含“服务不可用”异常的已知原因和解决方案。

### <a name="the-required-ports-are-being-blocked"></a>所需端口被阻止
验证所有[必需的端口](performance-tips-dotnet-sdk-v3-sql.md#networking)是否已启用。

### <a name="client-side-transient-connectivity-issues"></a>客户端暂时性连接问题
当存在导致超时的暂时性连接问题时，可能会出现“服务不可用”异常。 通常，与此情况相关的堆栈跟踪将包含 `TransportException` 错误。 例如：

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

请按照[请求超时故障排除步骤](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)解决此问题。

### <a name="service-outage"></a>服务中断
检查 [Azure 状态](https://status.azure.com/status)，了解是否有正在发生的问题。


## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。