---
title: "Microsoft Azure 中继异常以及解决方法 | Microsoft Docs"
description: "Azure 中继异常和建议的操作列表。"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: bc71995deef3f9795ebd33d9642c7ffa1995021b
ms.openlocfilehash: 346851226dce8ca8e48d3ae291b97e6e43d1ae03


---
# <a name="relay-exceptions"></a>中继异常

本文列出了 Microsoft Azure 中继 API 生成的一些异常。 这些参考信息可随时更改，请不时返回查看更新内容。

## <a name="exception-categories"></a>异常类别

中继 API 会生成以下类别的异常，以及在尝试修复这些异常时可以采取的相关操作。

1. 用户代码错误（[System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、[System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、[System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、[System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)）。 常规操作：继续之前尝试修复代码。
2. 设置/配置错误（[Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)、[System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)）。 常规操作：检查你的配置，必要时进行更改。
3. 暂时性异常（[Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)）。 常规操作：重试操作或通知用户。
4. 其他异常（[System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)）。 常规操作：特定于异常类型；请参考以下部分中的表。 

## <a name="exception-types"></a>异常类型

下表列出了消息异常的类型及其原因，并说明你可以采取的建议性操作。

| **异常类型** | **说明** | **建议的操作** | **自动或立即重试注意事项** |
| --- | --- | --- | --- |
| [超时](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |服务器在 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout) 控制的指定时间内未响应请求的操作。 服务器可能已完成请求的操作。 这可能是由于网络或其他基础结构延迟造成的。 |检查系统状态的一致性，并根据需要重试。 请参阅[超时异常](#timeoutexception)。 |在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [操作无效](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |不允许在服务器或服务中执行请求的用户操作。 有关详细信息，请查看异常消息。 例如，如果消息是在 **ReceiveAndDelete** 模式下收到的，则 [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) 将生成此异常。 |检查代码和文档。 确保请求的操作有效。 |重试不会解决问题。 |
| [操作已取消](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |尝试对已关闭、中止或释放的对象调用某个操作。 在极少数的情况下，已释放环境事务。 |检查代码并确保代码不会对已释放的对象调用操作。 |重试不会解决问题。 |
| [未授权访问](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 对象无法获取令牌，该令牌无效，或者令牌不包含执行操作所需的声明。 |确保使用正确的值创建令牌提供程序。 检查访问控制服务的配置。 |在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [参数异常](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [参数为 Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[参数超出范围](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |提供给该方法的一个或多个参数均无效。<br /> 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) 的 URI 包含路径段。<br /> 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) 的 URI 方案无效。 <br />属性值大于 32KB。 |检查调用代码并确保参数正确。 |重试不会解决问题。 |
| [服务器忙](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |服务目前无法处理请求。 |客户端可以等待一段时间，然后重试操作。 |客户端可在特定的时间间隔后重试操作。 如果重试导致其他异常，请检查该异常的重试行为。 |
| [超出配额](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |消息实体已达到其最大允许大小。 |通过从实体或其子队列接收消息在该实体中创建空间。 请参阅[QuotaExceededException](#quotaexceededexception)。 |如果同时已删除消息，则重试可能会有帮助。 |
| [超出消息大小](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |消息负载超出 256K 限制。 请注意，256K 限制是指总消息大小，可能包括系统属性和任何 .NET 开销。 |减少消息负载的大小，然后重试操作。 |重试不会解决问题。 |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) 指示已超出某个特定实体的配额。

对于中继，此异常包含 [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx)，指示已超过此终结点的最大侦听器数目。 这会以异常消息的 **MaximumListenersPerEndpoint** 值表示。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 指示用户启动的操作所用的时间超过操作超时值。 

应检查 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 属性的值，因为达到此限制也会导致 [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 异常。

对于中继，可能会在第一次打开中继发送方连接时收到超时异常。 此异常有两个常见的原因：

1. [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 值可能太小（甚至几分之一秒）。
2. 本地中继侦听器可能无响应（或者可能遇到禁止侦听器接受新客户端连接的防火墙规则问题），而 [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 值约小于 20 秒。

例如：

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>常见原因
此错误有两个常见的原因：配置不正确或暂时性服务错误。

1. **配置不正确**
   运行条件下的操作超时值可能太小。 客户端 SDK 的操作超时默认值为 60 秒。 请查看代码是否将该值设置得过小。 请注意，网络和 CPU 使用率的状况会影响完成特定操作所用的时间，因此，操作超时不应设置为非常小的值。
2. **暂时性服务错误**
   有时，中继在处理请求时会遇到延迟，例如，高流量时段。 在这种情况下，你可以在延迟后重试操作，直到操作成功为止。 如果多次尝试同一操作后仍然失败，请访问 [Azure 服务状态站点](https://azure.microsoft.com/status/)，看是否有任何已知的服务中断。

## <a name="next-steps"></a>后续步骤：
* [中继常见问题](relay-faq.md)
* [创建命名空间](relay-create-namespace-portal.md)
* [.NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Feb17_HO1-->


