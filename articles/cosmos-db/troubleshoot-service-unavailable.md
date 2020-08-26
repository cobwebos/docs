---
title: 排查 Azure Cosmos DB 服务不可用异常
description: 如何诊断和修复 Cosmos DB 服务不可用异常
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987369"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>诊断和故障排除 Azure Cosmos DB 服务不可用
SDK 无法连接到 Azure Cosmos DB 服务。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含有关服务不可用异常的已知原因和解决方案。

### <a name="1-the-required-ports-are-being-blocked"></a>1. 所需端口被阻止
验证是否已启用所有[所需的端口](performance-tips-dotnet-sdk-v3-sql.md#networking)。

### <a name="2-client-side-transient-connectivity-issues"></a>2. 客户端暂时性连接问题
当存在导致超时的暂时性连接问题时，服务不可用异常表面可能会出现问题。 通常，与此方案相关的堆栈跟踪将包含 `TransportException` ，例如：

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

请按照[请求超时疑难解答](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)进行解决。

### <a name="3-service-outage"></a>3. 服务中断
检查[Azure 状态](https://status.azure.com/status)以查看是否存在持续问题。


## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则