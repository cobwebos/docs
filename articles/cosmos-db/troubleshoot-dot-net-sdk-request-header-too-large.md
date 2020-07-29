---
title: Azure Cosmos DB 中的请求标头的故障排除太大或400错误请求
description: 如何诊断和修复请求标头太大异常
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294049"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>诊断并解决 Azure Cosmos DB 请求标头太大消息
使用 HTTP 错误代码400引发请求标头太大的消息。 如果请求标头的大小增长过大，并且超出了允许的最大大小，则会发生此错误。 建议使用最新版本的 SDK。 请确保至少使用版本1.x 或2.x，因为这些版本向异常消息添加标头大小跟踪。

## <a name="troubleshooting-steps"></a>疑难解答步骤
如果会话或继续标记太大，则会出现 "请求标头太大" 消息。 以下各节介绍了每个类别中的问题的原因和解决方法。

### <a name="1-session-token-too-large"></a>1. 会话令牌太大

#### <a name="cause"></a>原因：
400请求错误很可能是由于会话令牌太大导致的。 如果以下语句为 true，则它将确认会话令牌太大。

* 错误发生在点操作上，如创建、读取、更新等，其中没有继续标记。
* 异常启动时不会对应用程序进行任何更改。 会话标记随着容器中分区数量的增加而增大。 随着数据量增加或吞吐量增加，分区数量会增加。

#### <a name="temporary-mitigation"></a>临时缓解： 
重新启动客户端应用程序以重置所有会话令牌。 会话令牌最终将增加到导致问题的以前的大小。 因此，请使用下一节中的解决方案来完全避免此问题。

#### <a name="solution"></a>解决方案：
1. 按照[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)或[.net V2](performance-tips.md)性能提示一文中的指南进行操作，然后将应用程序转换为使用带有 TCP 协议的直接连接模式。 带有 TCP 协议的直接模式没有标头大小限制，如 HTTP 协议，因此可避免此问题。 请确保使用最新版本的 SDK，此版本在服务互操作不可用时可以修复查询操作。
2. 如果没有用于工作负荷的直接连接模式，请通过更改[客户端一致性级别](how-to-manage-consistency.md)来缓解这种情况。 会话令牌仅用于会话一致性，这是 Azure Cosmos DB 的默认一致性级别。 其他一致性级别不使用会话令牌。

### <a name="2-continuation-token-too-large"></a>2. 继续标记太大

#### <a name="cause"></a>原因：
使用继续标记的查询操作发生了400错误的请求。 如果继续标记的增长太大或不同的查询具有不同的继续标记大小，则为。
    
#### <a name="solution"></a>解决方案：
1. 按照[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)或[.net V2](performance-tips.md)性能提示一文中的指南进行操作，然后将应用程序转换为使用带有 TCP 协议的直接连接模式。 带有 TCP 协议的直接模式没有标头大小限制，如 HTTP 协议，因此可避免此问题。 
3. 如果没有用于工作负荷的直接连接模式，请尝试设置 `ResponseContinuationTokenLimitInKb` 选项。 `FeedOptions`对于 v2 或 v3 中的，你可以在中找到此选项 `QueryRequestOptions` 。

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则
