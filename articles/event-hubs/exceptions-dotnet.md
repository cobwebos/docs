---
title: Azure 事件中心-.NET 异常
description: 本文提供 Azure 事件中心 .NET 消息传送异常和建议的操作列表。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: shvija
ms.openlocfilehash: 6a1d7c969d31033ae9d00d212cc8f1a45abbeda1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341720"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
当特定于事件中心的操作导致了问题时，将触发 **EventHubsException** ，包括服务中的错误和特定于客户端的错误。 

## <a name="exception-information"></a>异常信息
该异常包括以下上下文信息，以帮助了解错误的上下文及其相对严重性。 

- **IsTransient**：标识异常是否被视为可恢复。 在被视为暂时性的情况下，已应用适当的重试策略，但重试不成功。
- **原因**：为故障提供一组众所周知的原因，这些原因有助于分类和阐明根本原因。 这些原因旨在允许在检查异常消息文本时应用异常筛选和其他逻辑。 一些关键的失败原因包括：
    - **客户端已关闭**：在已关闭或已释放的事件中心客户端时发生。 建议你检查应用程序代码，以确保在预期范围内创建并关闭事件中心客户端库中的对象。
    - **服务超时**：指示事件中心服务未在预期的时间内响应操作。 此问题可能是由暂时性的网络问题或服务问题引起的。 事件中心服务可能已成功完成请求，也可能未成功完成。状态未知。 建议尝试验证当前状态并重试（如有必要）。
    - **超过配额**：指示单个使用者组的活动读取操作太多。 此限制取决于事件中心命名空间的层，可能需要移动到更高的层。 另一种方法是创建其他使用者组，并确保任何组的使用者客户端读取次数在限制范围内。 有关详细信息，请参阅 [Azure 事件中心配额和限制](event-hubs-quotas.md)。
    - **超出消息大小**：事件数据作为单个事件和一批事件允许的最大大小。 它包括事件的数据以及任何关联的元数据和系统开销。 若要解决此错误，请减少批次发送的事件数或减少消息中包含的数据的大小。 由于大小限制可能会更改，因此请参阅 [Azure 事件中心配额和细节限制](event-hubs-quotas.md) 。
    - **使用者断开连接**：事件中心服务已从事件中心实例断开使用者客户端。 这种情况通常发生在所有者级别更高的使用者通过分区和使用者组配对断言所有权。
    - **找不到资源**：事件中心服务找不到事件中心、使用者组或分区等资源。 它可能已被删除，或者事件中心服务本身存在问题。

## <a name="handling-exceptions"></a>处理异常
可以通过多种方式对 **EventHubException**  的特定失败原因做出反应。 一种方式是在 catch 块中应用异常筛选子句。

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>后续步骤
[本文](event-hubs-messaging-exceptions.md)还介绍了其他一些例外。 其中一些仅适用于旧的事件中心 .NET 客户端库。