---
title: Azure Cosmos DB 中的 "请求标头太大" 消息或400错误请求的疑难解答
description: 了解如何诊断和修复 "请求标头太大" 异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a4d8a919d1881f61e490f135cc8fb1659c64cbd3
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871116"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>诊断并解决 Azure Cosmos DB "请求标头太大" 消息
使用 HTTP 错误代码400引发 "请求标头太大" 消息。 如果请求标头的大小增长得太大以致超出了允许的最大大小，则会发生此错误。 建议使用最新版本的 SDK。 至少使用版本1.x 或2.x，因为这些版本向异常消息添加标头大小跟踪。

## <a name="troubleshooting-steps"></a>疑难解答步骤
如果会话或继续标记太大，则会出现 "请求标头太大" 消息。 以下各节介绍了每个类别中的问题及其解决方案的原因。

### <a name="session-token-is-too-large"></a>会话令牌太大

#### <a name="cause"></a>原因：
由于会话令牌太大，可能会出现400错误的请求。 如果以下语句为 true，则会话令牌太大：

* 错误发生在不存在继续标记的点操作上，如 create、read 和 update。
* 异常启动时没有对应用程序进行任何更改。 会话令牌随着容器中分区数量的增加而增加。 随着数据量的增加或吞吐量增加，分区数量会增加。

#### <a name="temporary-mitigation"></a>临时缓解： 
重启你的客户端应用程序以重置所有会话标记。 最终，会话令牌会增长为先前导致问题的大小。 若要完全避免此问题，请使用下一部分中的解决方案。

#### <a name="solution"></a>解决方案：
1. 按照 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 或 [.net v2](performance-tips.md) 性能提示文章中的指导进行操作。 将应用程序转换为使用传输控制协议的直接连接模式 (TCP) 。 带有 TCP 协议的直接连接模式没有标头大小限制，如 HTTP 协议，因此可避免此问题。 请确保使用最新版本的 SDK，此版本在服务互操作不可用时可以修复查询操作。
1. 如果使用 TCP 协议的直接连接模式不是工作负荷的选项，请通过更改 [客户端一致性级别](how-to-manage-consistency.md)来缓解此情况。 会话标记仅用于实现会话一致性（这是 Azure Cosmos DB 的默认一致性级别）。 其他一致性级别不使用会话令牌。

### <a name="continuation-token-is-too-large"></a>继续标记太大

#### <a name="cause"></a>原因：
如果继续标记的增长太大或不同的查询具有不同的继续标记大小，则查询操作将发生400错误的请求。
    
#### <a name="solution"></a>解决方案：
1. 按照 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 或 [.net v2](performance-tips.md) 性能提示文章中的指导进行操作。 将应用程序转换为使用带有 TCP 协议的直接连接模式。 带有 TCP 协议的直接连接模式没有标头大小限制，如 HTTP 协议，因此可避免此问题。 
1. 如果使用 TCP 协议的直接连接模式不是工作负荷的选项，请设置 `ResponseContinuationTokenLimitInKb` 选项。 可以在 `FeedOptions` v2 或 v3 中找到此选项 `QueryRequestOptions` 。

## <a name="next-steps"></a>后续步骤
* [诊断并解决](troubleshoot-dot-net-sdk.md) 使用 AZURE COSMOS DB .net SDK 时遇到的问题。
* 了解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的性能准则。
