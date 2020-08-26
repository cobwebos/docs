---
title: 排除 Azure Cosmos DB 服务不可用异常
description: 了解如何诊断和修复 Azure Cosmos DB 服务不可用异常。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870861"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>诊断和解决 Azure Cosmos DB 服务不可用异常
SDK 无法连接到 Azure Cosmos DB。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含“服务不可用”异常的已知原因和解决方案。

### <a name="the-required-ports-are-being-blocked"></a>所需端口被阻止
验证所有[必需的端口](performance-tips-dotnet-sdk-v3-sql.md#networking)是否已启用。

### <a name="client-side-transient-connectivity-issues"></a>客户端暂时性连接问题
当存在导致超时的暂时性连接问题时，服务不可用异常可能会出现问题。 通常，与此方案相关的堆栈跟踪将包含 `TransportException` 错误。 例如：

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

请按照 [请求超时故障排除步骤](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) 来解决此问题。

### <a name="service-outage"></a>服务中断
检查 [Azure 状态](https://status.azure.com/status) 以查看是否存在持续问题。


## <a name="next-steps"></a>后续步骤
* [诊断并解决](troubleshoot-dot-net-sdk.md) 使用 AZURE COSMOS DB .net SDK 时遇到的问题。
* 了解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的性能准则。