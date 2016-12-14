---
title: "如何通过 Python 使用服务总线队列 | Microsoft 文档"
description: "了解如何使用 Python 中的 Azure 服务总线队列"
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/21/2016
ms.author: sethm;lmazuel
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a162dc04f8eb5002cae3bf708ae2fcd4c2aa694


---
# <a name="how-to-use-service-bus-queues"></a>如何使用 Service Bus 队列
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文介绍了如何使用服务总线队列。 相关示例采用 Python 编写，并使用了 [Python Azure 服务总线包][Python Azure 服务总线包]。 涉及的任务包括**创建队列、发送和接收消息**以及**删除队列**。

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [!NOTE]
> 若要安装 Python 或 [Python Azure 服务总线包][Python Azure 服务总线包]，请参阅 [Python 安装指南](../python-how-to-install.md)。
> 
> 

## <a name="create-a-queue"></a>创建队列
可以通过 **ServiceBusService** 对象处理队列。 将以下代码添加到任何 Python 文件的顶部附近，你希望在其中以编程方式访问服务总线：

```
from azure.servicebus import ServiceBusService, Message, Queue
```

以下代码创建 **ServiceBusService** 对象。 将 `mynamespace`、`sharedaccesskeyname` 和 `sharedaccesskey` 替换为你的命名空间、共享访问签名 (SAS) 密钥名称和值。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

SAS 密钥名称和值可以在 [Azure 经典门户][Azure 经典门户]连接信息中找到，也可以在服务器资源管理器中选择服务总线命名空间后，在 Visual Studio“**属性**”窗格中找到（如前一部分中所示）。

```
bus_service.create_queue('taskqueue')
```

**create_queue** 还支持其他选项，通过这些选项可以重写默认队列设置，例如消息生存时间 (TTL) 或最大队列大小。 以下示例将最大队列大小设置为 5GB，将 TTL 值设置为 1 分钟：

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息
若要向服务总线队列发送消息，应用程序需对 **ServiceBusService** 对象调用 **send\_queue\_message** 方法。

下面的示例演示如何使用 **send\_queue\_message** 向名为“taskqueue”的队列发送一条测试消息：

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大为 64 KB，其中包括标准和自定义应用程序属性。 一个队列可包含的消息数不受限制，但消息的总大小受限。 此队列大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额][服务总线配额]。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
对 **ServiceBusService** 对象使用 **receive\_queue\_message**方法可从队列接收消息：

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

**peek\_lock** 参数设置为 **False** 时，将在读取消息后将其从队列中删除。 通过将参数 **peek\_lock** 设置为 **True**，可读取（扫视）并锁定消息而不会将其从队列中删除。

在接收过程中读取并删除消息的行为是最简单的模式，并且最适合在发生故障时应用程序可以容忍不处理消息的情况。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于 Service Bus 会将消息标记为“将使用”，因此当应用程序重启并重新开始使用消息时，它会丢失在发生崩溃前使用的消息。

如果将 **peek\_lock** 参数设置为 **True**，则接收将变成一个两阶段操作，从而可支持无法容忍遗漏消息的应用程序。 当 Service Bus 收到请求时，它会查找下一条要使用的消息，锁定该消息以防其他使用者接收，然后将该消息返回到应用程序。 应用程序处理完消息（或安全存储该消息以供将来处理）后，会通过对 **Message** 对象调用 **delete** 方法来完成接收过程的第二个阶段。 **delete** 方法会将消息标记为已使用，并从队列中将其删除。

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何处理应用程序崩溃和不可读消息
Service Bus 提供了相关功能来帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则可对 **Message** 对象调用 **unlock** 方法。 这将导致 Service Bus 解锁队列中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

还存在与队列中已锁定消息关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），Service Bus 将自动解锁该消息并使它可再次被接收。

如果应用程序在处理消息之后，但在调用 **delete** 方法之前崩溃，则在应用程序重新启动时会将该消息重新传送给它。 此情况通常称作**至少处理一次**，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。 这通常可以通过使用消息的 **MessageId** 属性来实现，该属性在多次传送尝试中保持不变。

## <a name="next-steps"></a>后续步骤
现在，你已了解有关服务总线队列的基础知识，单击下面的链接可了解更多信息。

* [队列、主题和订阅][队列、主题和订阅]。

[Azure 经典门户]: https://manage.windowsazure.com
[Python Azure 服务总线包]: https://pypi.python.org/pypi/azure-servicebus  
[队列、主题和订阅]: service-bus-queues-topics-subscriptions.md
[服务总线配额]: service-bus-quotas.md




<!--HONumber=Nov16_HO3-->


