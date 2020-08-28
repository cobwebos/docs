---
title: 如何在 .NET SDK 中转换会话令牌格式 - Azure Cosmos DB
description: 了解如何转换会话令牌格式，以确保不同 .NET SDK 版本之间的兼容性
author: vinhms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 787c39681d0e9aff25d205c7b195be00b8c0bc9c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020008"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>在 .NET SDK 中转换会话令牌格式

本文介绍如何在不同会话令牌格式之间进行转换，以确保 SDK 版本之间的兼容性。

> [!NOTE]
> 默认情况下，SDK 会自动跟踪会话令牌，并会使用最新的会话令牌。  有关详细信息，请访问[利用会话令牌](how-to-manage-consistency.md#utilize-session-tokens)。 本文中的说明仅适用于以下情况：
> * Azure Cosmos DB 帐户使用会话一致性。
> * 你手动管理会话令牌。
> * 你同时使用了多个版本的 SDK。

## <a name="session-token-formats"></a>会话令牌格式

有两种会话令牌格式：“简单”和“向量”。  这两种格式不能互换，因此，在将会话令牌传递到不同版本的客户端应用程序时，应转换格式。
- .NET SDK V1（Microsoft.Azure.DocumentDB - 版本 1.x）使用“简单”会话令牌格式。
- .NET SDK V2（Microsoft.Azure.DocumentDB - 版本 2.x）使用“向量”会话令牌格式。

### <a name="simple-session-token"></a>简单会话令牌

简单会话令牌采用此格式：`{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>向量会话令牌

向量会话令牌采用以下格式：`{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>转换为“简单”会话令牌

若要使用 .NET SDK V1 将会话令牌传递给客户端，请使用“简单”会话令牌格式。  例如，使用下面的示例代码来转换会话令牌。

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>转换为“向量”会话令牌

若要使用 .NET SDK V2 将会话令牌传递给客户端，请使用“向量”会话令牌格式。  例如，使用下面的示例代码来转换会话令牌。

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>后续步骤

请阅读以下文章：

* [使用会话令牌在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [在 Azure Cosmos DB 中选择适当的一致性级别](consistency-levels-choosing.md)
* [Azure Cosmos DB 中的一致性、可用性和性能权衡](consistency-levels-tradeoffs.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
