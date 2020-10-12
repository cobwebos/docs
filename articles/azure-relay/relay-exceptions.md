---
title: Azure 中继异常以及解决方法 | Microsoft Docs
description: Azure 中继异常以及解决这些异常应采取的建议操作的列表。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a644dfe80255c64980400866a5e3d197f75375bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87532962"
---
# <a name="azure-relay-exceptions"></a>Azure 中继异常

本文列出了可能由 Azure 中继 API 生成的一些异常。 这些参考信息可随时更改，请不时返回查看更新内容。

## <a name="exception-categories"></a>异常类别

中继 API 生成的异常可以分为以下类别。 此外还列出了解决这些异常应采取的建议操作。

*   **用户编码错误**：[System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1)、[System.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1)、[System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1)、[System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1)。 

    常规操作：继续之前尝试修复代码。
*   **设置/配置错误**：[System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1)。 

    常规操作：检查配置。 必要时更改配置。
*   **暂时性异常**：[Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)。 

    常规操作：重试操作或通知用户。
*   **其他异常**：[System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1)、[System.TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1)。 

    **常规操作**：特定于异常类型。 请参阅以下部分中的表。 

## <a name="exception-types"></a>异常类型

下表列出了消息异常的类型及其原因， 并说明可以采取的有助于解决异常的建议性操作。

| **异常类型** | **说明** | **建议的操作** | **自动或立即重试注意事项** |
| --- | --- | --- | --- |
| [超时](/dotnet/api/system.timeoutexception?view=netcore-3.1) |服务器在 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout) 控制的指定时间内未响应请求的操作。 服务器可能已完成请求的操作。 这可能是由于网络或其他基础结构延迟造成的。 |检查系统状态的一致性，并根据需要重试。 请参阅 [TimeoutException](#timeoutexception)。 |在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [操作无效](/dotnet/api/system.invalidoperationexception?view=netcore-3.1) |不允许在服务器或服务中执行请求的用户操作。 有关详细信息，请查看异常消息。 |检查代码和文档。 确保请求的操作有效。 |重试不会解决问题。 |
| [操作已取消](/dotnet/api/system.operationcanceledexception?view=netcore-3.1) |尝试对已关闭、中止或释放的对象调用某个操作。 在极少数的情况下，已释放环境事务。 |检查代码并确保代码不会对已释放的对象调用操作。 |重试不会解决问题。 |
| [未授权访问](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 对象无法获取令牌，该令牌无效，或者令牌不包含执行操作所需的声明。 |确保使用正确的值创建令牌提供程序。 检查访问控制服务的配置。 |在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [参数异常](/dotnet/api/system.argumentexception?view=netcore-3.1)；<br /> [参数为 Null](/dotnet/api/system.argumentnullexception?view=netcore-3.1)；<br />[参数超出范围](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1) |出现下述一个或多个情况：<br />提供给该方法的一个或多个参数均无效。<br /> 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) 的 URI 包含一个或多个路径段。<br />提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) 的 URI 方案无效。 <br />属性值大于 32 KB。 |检查调用代码并确保参数正确。 |重试不会解决问题。 |
| [服务器忙](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |服务目前无法处理请求。 |客户端可以等待一段时间，并重试操作。 |客户端可以在特定的时间间隔后重试。 如果重试导致其他异常，请检查该异常的重试行为。 |
| [超出配额](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |消息实体已达到其最大允许大小。 |通过从实体或其子队列接收消息在该实体中创建空间。 请参阅[QuotaExceededException](#quotaexceededexception)。 |如果同时已删除消息，则重试可能会有帮助。 |
| [超出消息大小](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |消息有效负载超出 256 KB 限制。 请注意，256 KB 限制是指总消息大小。 总消息大小可能包括系统属性和任何 Microsoft .NET 开销。 |减少消息负载的大小，并重试操作。 |重试不会解决问题。 |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) 指示已超出某个特定实体的配额。

对于中继，此异常包含 [System.ServiceModel.QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception?view=dotnet-plat-ext-3.1)，指示已超过此终结点的最大侦听器数目。 这会以异常消息的 **MaximumListenersPerEndpoint** 值表示。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) 指示用户启动的操作所用的时间超过操作超时值。 

检查 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=netcore-3.1#System_Net_ServicePointManager_DefaultConnectionLimit) 属性的值。 达到此限制也可能导致 [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1)。

对于中继，可能会在第一次打开中继发送方连接时收到超时异常。 此异常有两个常见的原因：

*   [OpenTimeout](/previous-versions/) 值可能太小（甚至只有几分之一秒）。
* 本地中继侦听器可能无响应（或者可能遇到禁止侦听器接受新客户端连接的防火墙规则问题），而 [OpenTimeout](/previous-versions/) 值约小于 20 秒。

示例：

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>常见原因
此错误有两个常见的原因：

*   **配置不正确**
    
    运行条件下的操作超时值可能太小。 客户端 SDK 的操作超时默认值为 60 秒。 请查看代码中的值是否设置过小。 请注意，CPU 使用率和网络条件可能会影响操作完成时间。 最好是不要将操作超时设置为很小的值。
*   **临时服务错误**

    有时，中继服务在处理请求时可能会遇到延迟。 例如，在流量高峰时段可能会发生这种情况。 如果发生这种情况，可以在延迟后重试操作，直到操作成功为止。 如果多次尝试同一操作后仍然失败，请访问 [Azure 服务状态站点](https://azure.microsoft.com/status/)，看是否有已知的服务中断。

## <a name="next-steps"></a>后续步骤
* [Azure 中继常见问题](relay-faq.md)
* [创建中继命名空间](relay-create-namespace-portal.md)
* [Azure 中继和 .NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [Azure 中继和 Node 入门](relay-hybrid-connections-node-get-started.md)
