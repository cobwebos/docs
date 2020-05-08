---
title: Azure 服务总线-消息传递异常 |Microsoft Docs
description: 本文提供了 Azure 服务总线消息传送异常以及发生异常时建议采取的措施的列表。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: f1a4caf6ffd5740b4227aff2f38d9cb709c77b48
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739341"
---
# <a name="service-bus-messaging-exceptions"></a>服务总线消息传送异常
本文列出了 .NET Framework Api 生成的 .NET 异常。 

## <a name="exception-categories"></a>异常类别
消息传送 API 会生成以下类别的异常，以及在尝试修复这些异常时可以采取的相关操作。 异常的含义和原因会因消息传送实体的类型而异：

1. 用户编码错误（[ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、 [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、 [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、 [SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)）。 "）。 常规操作：继续之前尝试修复代码。
2. 设置/配置错误（[microsoft.servicebus.messaging.messagingentitynotfoundexception](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception)、 [system.unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)。 常规操作：检查配置，必要时进行更改。
3. 暂时性异常（[MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、 [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception)、 [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)）。）的情况下，会发生这种情况。 常规操作：重试操作或通知用户。 客户端 SDK 中的 `RetryPolicy` 类可以配置为自动处理重试。 有关详细信息，请参阅[重试指南](/azure/architecture/best-practices/retry-service-specific#service-bus)。
4. 其他异常（[TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)， [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)，Microsoft.servicebus.messaging.messagelocklostexception，Microsoft.servicebus.messaging.messagelocklostexception、microsoft.servicebus.messaging.sessionlocklostexception，， [，](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)， [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)）。 常规操作：特定于异常类型；请参考以下部分中的表： 

## <a name="exception-types"></a>异常类型
下表列出了消息异常的类型及其原因，并说明可以采取的建议性操作。

| **异常类型** | **说明/原因/示例** | **建议的操作** | **自动/立即重试注意事项** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |服务器在 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 控制的指定时间内未响应请求的操作。 服务器可能已完成请求的操作。 这可能是由于网络或其他基础结构延迟造成的。 |检查系统状态的一致性，并根据需要重试。 请参阅[超时异常](#timeoutexception)。 |在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |不允许在服务器或服务中执行请求的用户操作。 有关详细信息，请查看异常消息。 例如，如果在[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)模式下收到消息，则[Complete （）](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)将生成此异常。 |检查代码和文档。 确保请求的操作有效。 |重试不起作用。 |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |尝试对已关闭、中止或释放的对象调用某个操作。 在极少数的情况下，已释放环境事务。 |检查代码并确保代码不会对已释放的对象调用操作。 |重试不起作用。 |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 对象无法获取令牌，该令牌无效，或者令牌不包含执行操作所需的声明。 |确保使用正确的值创建令牌提供程序。 检查访问控制服务的配置。 |在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |提供给该方法的一个或多个参数均无效。<br /> 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 包含路径段。<br /> 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 方案无效。 <br />属性值大于 32 KB。 |检查调用代码并确保参数正确。 |重试不起作用。 |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |与操作关联的实体不存在或已被删除。 |确保该实体存在。 |重试不起作用。 |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |尝试接收具有特定序列号的消息。 找不到此消息。 |确保该消息尚未接收。 检查死信队列，以确定该消息是否被视为死信。 |重试不起作用。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |客户端无法与服务总线建立连接。 |确保提供的主机名正确并且主机可访问。 |如果存在间歇性的连接问题，重试可能会有帮助。 |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |服务目前无法处理请求。 |客户端可以等待一段时间，并重试操作。 |客户端可在特定的时间间隔后重试操作。 如果重试导致其他异常，请检查该异常的重试行为。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |在以下情况下，可能会引发一般消息异常：<p>尝试使用属于其他实体类型（例如主题）的名称或路径创建 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)。</p><p>尝试发送大于 256 KB 的消息。 </p>服务器或服务在处理请求期间遇到错误。 有关详细信息，请查看异常消息。 这通常是暂时性异常。</p><p>请求已终止，因为该实体被限制。 错误代码：50001、50002、50008。 </p> | 检查代码，并确保只对消息正文使用可序列化对象（或使用自定义序列化程序）。 <p>在文档中查看属性支持的值类型，并只使用支持的类型。</p><p> 检查 [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 属性。 如果为 true，可以重试操作****。 </p>| 如果异常是由于限制导致的，请等待几秒钟，然后重试该操作。 重试行为是未定义的，在其他情况下可能不会有帮助。|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |尝试使用已被该服务命名空间中另一实体使用的名称创建实体。 |删除现有的实体，或者选择不同的名称来创建实体。 |重试不起作用。 |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |消息实体已达到其允许的最大大小，或已超出到命名空间的最大连接数。 |通过从实体或其子队列接收消息在该实体中创建空间。 请参阅[QuotaExceededException](#quotaexceededexception)。 |如果同时已删除消息，则重试可能会有帮助。 |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |如果尝试创建无效的规则操作，服务总线将返回此异常。 如果在处理该消息的规则操作时出错，服务总线会将此异常附加到死信消息。 |检查规则操作是否正确。 |重试不起作用。 |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |如果尝试创建无效的筛选器，服务总线将返回此异常。 如果在处理该消息的筛选器时出错，服务总线会将此异常附加到死信消息。 |检查筛选器是否正确。 |重试不起作用。 |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |尝试接受具有特定会话 ID 的会话，但该会话当前已被另一客户端锁定。 |确保该会话未由其他客户端锁定。 |如果在此期间会话已释放，则重试可能会有帮助。 |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |事务包含过多的操作。 |减少此事务中操作的数目。 |重试不起作用。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |对已禁用的实体请求运行时操作。 |激活实体。 |如果在此期间该实体已激活，则重试可能会有帮助。 |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |如果向已启用预筛选的主题发送消息并且所有筛选器都不匹配，则服务总线返回此异常。 |确保至少有一个筛选器匹配。 |重试不起作用。 |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |消息有效负载超出 256 KB 限制。 256-KB 限制是指总消息大小，可能包括系统属性和任何 .NET 开销。 |减少消息负载的大小，并重试操作。 |重试不起作用。 |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |环境事务 (*Transaction.Current*) 无效。 该事务可能已完成或已中止。 内部异常可能会提供其他信息。 | |重试不起作用。 |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |已对未决事务尝试进行操作，或尝试提交该事务并且事务进入不确定状态。 |应用程序必须处理此异常（作为特例），因为此事务可能已提交。 |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception)指示已超出某个特定实体的配额。

### <a name="queues-and-topics"></a>队列和主题
对队列和主题而言，这通常指队列的大小。 错误消息属性会包含更多详细信息，如以下示例所示：

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

该消息指出，主题超过其大小限制，本例中为 1 GB（默认大小限制）。 

### <a name="namespaces"></a>命名空间

对于命名空间，[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 可指示应用程序已超出到命名空间的最大连接数。 例如：

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>常见原因
此错误有两个常见的原因：死信队列和无法正常运行的消息接收器。

1. **[死信队列](service-bus-dead-letter-queues.md)** 读取器无法完成消息，当锁定过期时，消息将返回到队列/主题。 如果读取器发生异常，以致无法调用 [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)，就会出现这种情况。 消息读取 10 次后，将默认移至死信队列。 此行为由 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 属性控制，默认值为 10。 消息堆积在死信队列中会占用空间。
   
    若要解决此问题，请读取并完成死信队列中的消息，就像处理任何其他队列一样。 可以使用 [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) 方法帮助格式化死信队列路径。
2. **接收方已停止**。 接收方已停止从队列或订阅接收消息。 识别这种情况的方法是查看 [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 属性，它会显示消息的完整细目。 如果 [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) 属性很高或不断增加，则表示消息写入的速度超过读取的速度。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 指示用户启动的操作所用的时间超过操作超时值。 

应检查 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 属性的值，因为达到此限制也会导致 [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 异常。

### <a name="queues-and-topics"></a>队列和主题
对于队列和主题，超时在 [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 属性中作为连接字符串的一部分指定，或通过 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) 指定。 错误消息本身可能会有所不同，但它始终包含当前操作的指定超时值。 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>原因

当使用[PeekLock](message-transfers-locks-settlement.md#peeklock)接收模式收到消息，并且客户端持有的锁在服务端过期时，将引发**microsoft.servicebus.messaging.messagelocklostexception** 。

由于各种原因，消息上的锁可能会过期- 

  * 锁定计时器已在客户端应用程序续订之前过期。
  * 客户端应用程序获取了该锁，并将其保存到持久性存储区中，然后重新启动。 重新启动后，客户端应用程序将查看即时消息并尝试完成这些消息。

### <a name="resolution"></a>解决方法

如果是**microsoft.servicebus.messaging.messagelocklostexception**，则客户端应用程序无法再处理该消息。 客户端应用程序可以选择考虑记录异常以进行分析，但客户端*必须*释放消息。

由于消息的锁已过期，它将返回到队列（或订阅），并可由调用 receive 的下一个客户端应用程序进行处理。

如果已超过**MaxDeliveryCount** ，则可能会将消息移动到**DeadLetterQueue**。

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>原因

当接受会话，并且客户端持有的锁在服务端过期时，将引发**microsoft.servicebus.messaging.messagelocklostexception、microsoft.servicebus.messaging.sessionlocklostexception** 。

由于各种原因，对会话的锁定可能会过期- 

  * 锁定计时器已在客户端应用程序续订之前过期。
  * 客户端应用程序获取了该锁，并将其保存到持久性存储区中，然后重新启动。 重新启动后，客户端应用程序将查看即时会话并尝试处理这些会话中的消息。

### <a name="resolution"></a>解决方法

如果是**microsoft.servicebus.messaging.messagelocklostexception、microsoft.servicebus.messaging.sessionlocklostexception**，则客户端应用程序无法再处理会话上的消息。 客户端应用程序可能会考虑记录要分析的异常，但是客户端*必须*释放消息。

由于会话的锁已过期，它将返回到队列（或订阅），并可由接受会话的下一个客户端应用程序锁定。 由于在任意给定时间，会话锁由单个客户端应用程序持有，因此可保证按顺序处理。

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>原因

以下情况下会引发**SocketException** -
   * 当连接尝试由于主机在指定时间之后未正确响应而失败时（TCP 错误代码10060）。
   * 建立的连接失败，因为连接的主机未能响应。
   * 处理消息时出错或远程主机超过超时。
   * 底层网络资源问题。

### <a name="resolution"></a>解决方法

**SocketException**错误表明承载应用程序的 VM 无法将该名称`<mynamespace>.servicebus.windows.net`转换为相应的 IP 地址。 

检查以下命令是否成功映射到 IP 地址。

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

这应提供如下所示的输出

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

如果上面的名称**未解析**为 IP 和命名空间别名，请检查网络管理员是否进一步进行调查。 名称解析是通过 DNS 服务器在客户网络中通常是一项资源来完成的。 如果 Azure DNS 进行 DNS 解析，请联系 Azure 支持部门。

如果名称解析**按预期方式工作**，请[在此处](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)检查是否允许连接到 Azure 服务总线


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>原因

**MessagingException**是一个通用异常，可能出于各种原因引发。 下面列出了其中的一些原因。

   * 尝试在**主题**或**订阅**上创建**QueueClient** 。
   * 发送的消息的大小超过了给定层的限制。 详细了解服务总线[配额和限制](service-bus-quotas.md)。
   * 由于限制，特定数据平面请求（发送、接收、完成、放弃）已终止。
   * 由于服务升级和重启导致的暂时性问题。

> [!NOTE]
> 上述异常列表并不完整。

### <a name="resolution"></a>解决方法

解决方法步骤取决于导致**MessagingException**引发的原因。

   * 对于**暂时性问题**（其中， ***isTransient***设置为***true***）或用于**阻止问题**，重试该操作可能会解决问题。 可以利用 SDK 上的默认重试策略。
   * 对于其他问题，异常中的详细信息指示可以从同一获取问题和解决步骤。

## <a name="next-steps"></a>后续步骤
有关服务总线 .NET API 的完整参考，请参阅 [Azure .NET API 参考](/dotnet/api/overview/azure/service-bus)。
有关疑难解答提示，请参阅[故障排除指南](service-bus-troubleshooting-guide.md)
