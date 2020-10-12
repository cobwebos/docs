---
title: 排查 Azure Cosmos DB 中的“请求标头太大”消息或 400 错误请求问题
description: 了解如何诊断和修复请求标头太大异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: d5d66ca05390af5f6fef91ca959f1db3d547d3fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89014041"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>诊断 Azure Cosmos DB“请求标头太大”消息并对其进行故障排除
引发了“请求标头太大”消息，并显示 HTTP 错误代码 400。 如果请求标头的大小变得太大以致超过了允许的最大值，则会发生此错误。 建议使用 SDK 的最新版本。 至少使用版本 3.x 或 2.x，因为这些版本会在异常消息中添加标头大小跟踪。

## <a name="troubleshooting-steps"></a>疑难解答步骤
如果会话令牌或延续令牌太大，则会出现“请求标头太大”消息。 以下各部分介绍了每个类别中的问题原因及其解决方案。

### <a name="session-token-is-too-large"></a>会话令牌太大

#### <a name="cause"></a>原因：
由于会话令牌太大，可能会出现 400 错误请求。 如果以下表述为真，则会话令牌太大：

* 此错误发生在其中没有延续令牌的点操作（例如创建、读取、更新等）上。
* 异常启动时没有对应用程序进行任何更改。 会话令牌随着容器中分区数量的增加而增加。 分区数量随着数据量的增加或吞吐量的增加而增加。

#### <a name="temporary-mitigation"></a>临时缓解： 
重启你的客户端应用程序以重置所有会话标记。 最终，会话令牌将恢复为导致该问题的先前大小。 若要完全避免此问题，请使用下一部分中的解决方案。

#### <a name="solution"></a>解决方案：
1. 请按照 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 或 [.NET v2 ](performance-tips.md) 性能提示文章中的指南进行操作。 将应用程序转换为使用具有传输控制协议 (TCP) 的直接连接模式。 采用 TCP 协议的直接连接模式对标头大小没有限制（HTTP 协议则有限制），因此可以避免此问题。 请确保使用最新版本的 SDK，此版本针对不可使用服务互操作时进行的查询操作提供了修复。
1. 如果采用 TCP 协议的直接连接模式不是适用于你的工作负载的选项，请通过更改[客户端一致性级别](how-to-manage-consistency.md)来缓解此情况。 会话标记仅用于实现会话一致性（这是 Azure Cosmos DB 的默认一致性级别）。 其他一致性级别不使用会话令牌。

### <a name="continuation-token-is-too-large"></a>延续令牌太大

#### <a name="cause"></a>原因：
如果延续令牌变得太大或不同的查询具有不同的延续令牌大小，则会在使用延续令牌的查询操作上发生 400 错误请求。
    
#### <a name="solution"></a>解决方案：
1. 请按照 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 或 [.NET v2 ](performance-tips.md) 性能提示文章中的指南进行操作。 将应用程序转换为使用具有 TCP 协议的直接连接模式。 采用 TCP 协议的直接连接模式对标头大小没有限制（HTTP 协议则有限制），因此可以避免此问题。 
1. 如果采用 TCP 协议的直接连接模式不是适用于你的工作负载的选项，请设置 `ResponseContinuationTokenLimitInKb` 选项。 可以在 v2 的 `FeedOptions` 或 v3 的 `QueryRequestOptions` 中找到此选项。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
