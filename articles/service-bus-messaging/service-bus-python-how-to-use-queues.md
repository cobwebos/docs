---
title: 快速入门：通过 Python 使用 Azure 服务总线队列
description: 本文介绍如何使用 Python 创建 Azure 服务总线队列，并向其发送消息和从中接收消息。
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: aa9ffc48d9b1374fa510f450bab2c66641421446
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773490"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>快速入门：通过 Python 使用 Azure 服务总线队列

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文介绍如何使用 Python 创建 Azure 服务总线队列，并向其发送消息和从中接收消息。 

有关 Python Azure 服务总线库的详细信息，请参阅[适用于 Python 的服务总线库](/python/api/overview/azure/servicebus?view=azure-python)。

## <a name="prerequisites"></a>必备条件
- Azure 订阅。 可以激活 [Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 遵循以下文章中的步骤创建的服务总线命名空间：[快速入门：使用 Azure 门户创建服务总线主题和订阅](service-bus-quickstart-topics-subscriptions-portal.md)。 复制“共享访问策略”屏幕中的主连接字符串，以便稍后在本文中使用。  
- 装有 [Python Azure 服务总线][Python Azure Service Bus package]包的 Python 3.4x 或更高版本。 有关详细信息，请参阅 [Python 安装指南](/azure/python/python-sdk-azure-install)。 

## <a name="create-a-queue"></a>创建队列

可通过某个 **ServiceBusService** 对象来使用队列。 若要以编程方式访问服务总线，请将以下行添加到 Python 文件的顶部附近：

```python
from azure.servicebus import ServiceBusClient
```

添加以下代码以创建 **ServiceBusClient** 对象。 请将 `<connectionstring>` 替换为服务总线的主连接字符串值。 可以在 [Azure 门户][Azure portal]上服务总线命名空间中的“共享访问策略”下找到此值。 

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

以下代码使用 **ServiceBusClient** 的 `create_queue` 方法创建名为 `taskqueue` 且采用默认设置的队列：

```python
sb_client.create_queue("taskqueue")
```

可以使用相应的选项来重写默认队列设置，例如消息生存时间 (TTL) 或最大主题大小。 以下代码创建名为 `taskqueue` 的队列，其最大队列大小为 5 GB，TTL 值为 1 分钟：

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息

若要向服务总线队列发送消息，应用程序需对 **ServiceBusService** 对象调用 `send` 方法。 以下代码示例创建一个队列客户端，并将测试消息发送到 `taskqueue` 队列。 请将 `<connectionstring>` 替换为服务总线的主连接字符串值。 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>消息大小限制和配额

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大大小为 64 KB，其中包括标准和自定义应用程序属性。 队列中可以包含的消息数量不受限制，但队列包含的消息总大小有上限。 可以在创建时定义队列大小，上限为 5 GB。 

有关配额的详细信息，请参阅[服务总线配额][Service Bus quotas]。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息

队列客户端通过对 **ServiceBusClient** 对象使用 `get_receiver` 方法来从队列接收消息。 以下代码示例创建一个队列客户端，并从 `taskqueue` 队列接收消息。 请将 `<connectionstring>` 替换为服务总线的主连接字符串值。 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>使用 peek_lock 参数

`get_receiver` 的 `peek_lock` 可选参数确定服务总线在从队列读取消息后是否删除消息。 默认的消息接收模式是 *PeekLock* 或设置为 **True** 的 `peek_lock`，后者在读取（扫视）后锁定消息，而不会从队列中删除消息。 然后，必须显式完成每个消息以将其从队列中删除。

若要在读取消息后将其从队列中删除，可将 `get_receiver` 的 `peek_lock` 参数设置为 **False**。 在执行接收操作过程中删除消息是最简单的模型，但仅当应用程序在发生失败的情况下能够容许消息缺失时，该模型才能正常工作。 为了理解此行为，可以设想这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 如果在接收消息时它已被删除，当应用程序重启并重新开始使用消息时，它便缺少了在发生崩溃之前收到的消息。

如果应用程序不能容许消息缺失，则接收过程是由两个阶段组成的操作。 PeekLock 查找要使用的下一个消息，将其锁定以防其他使用方接收它，然后将该消息返回给应用程序。 处理或存储消息后，应用程序通过对 **Message** 对象调用 `complete` 方法完成接收过程的第二个阶段。  `complete` 方法会将消息标记为已使用，并将其从队列中删除。

## <a name="handle-application-crashes-and-unreadable-messages"></a>处理应用程序崩溃和不可读消息

服务总线提供了相关功能，帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则可对 **Message** 对象调用 `unlock` 方法。 服务总线解锁队列中的消息，并使其能够重新被同一个或另一个使用方应用程序接收。

队列中锁定的消息还存在超时。 如果应用程序无法在锁定超时期满前处理消息（例如，如果应用程序崩溃），服务总线会自动解锁消息，让它再次可供接收。

如果应用程序在处理消息之后，但在调用 `complete` 方法之前崩溃，则在应用程序重启时会将该消息重新传送给它。 此行为通常称为“至少处理一次”。  每条消息将至少处理一次，但在某些情况下，可能会重新传送同一消息。 如果方案无法容许重复处理，可以使用消息的 **MessageId** 属性（多次尝试传送时，该属性保持不变）来处理重复消息传送。 

> [!TIP]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 可以使用服务总线资源管理器连接到服务总线命名空间并轻松管理消息传送实体。 该工具提供高级功能，例如导入/导出功能，以及用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。

## <a name="next-steps"></a>后续步骤

了解服务总线队列的基本信息后，请参阅[队列、主题和订阅][Queues, topics, and subscriptions]以获取更多信息。

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
