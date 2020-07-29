---
title: 排查 Azure Cosmos DB 服务不可用异常
description: 如何诊断和修复 Cosmos DB 服务不可用异常
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294014"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>诊断和故障排除 Azure Cosmos DB 服务不可用
SDK 无法连接到 Azure Cosmos DB 服务。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含有关服务不可用异常的已知原因和解决方案。

### <a name="1-the-required-ports-are-not-enabled"></a>1. 未启用所需的端口。
验证是否已启用所有[所需的端口](performance-tips-dotnet-sdk-v3-sql.md#networking)。

## <a name="if-an-existing-application-or-service-started-getting-503"></a>如果现有的应用程序或服务已开始获取503

### <a name="1-there-is-an-outage"></a>1. 发生服务中断
检查[Azure 状态](https://status.azure.com/status)以查看是否存在持续问题。

### <a name="2-snat-port-exhaustion"></a>2. SNAT 端口耗尽
遵循[SNAT 端口消耗指南](troubleshoot-dot-net-sdk.md#snat)。

### <a name="3-the-required-ports-are-being-blocked"></a>3. 所需端口被阻止
验证是否已启用所有[所需的端口](performance-tips-dotnet-sdk-v3-sql.md#networking)。

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则