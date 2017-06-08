---
title: "如何通过 Ruby 使用 Azure 服务总线队列 | Microsoft Docs"
description: "了解如何在 Azure 中使用 Service Bus 队列。 用 Ruby 编写的代码示例。"
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: b649bab171770b1edb3ed4b4e345375d948e6a97
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-use-service-bus-queues"></a>如何使用 Service Bus 队列
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本指南介绍如何使用服务总线队列。 相关示例通过 Ruby 编写并使用 Azure gem。 涉及的任务包括**创建队列、发送和接收消息**以及**删除队列**。 有关服务总线队列的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>如何创建队列
可以通过 **Azure::ServiceBusService** 对象处理队列。 若要创建队列，请使用 **create_queue()** 方法。 以下示例将创建一个队列或输出任何错误。

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

还可以通过其他选项传递 **Azure::ServiceBus::Queue** 对象，这些选项可让你重写默认队列设置，如消息保存时间或最大队列大小。 以下示例演示如何将最大队列大小设置为 5GB，将生存时间设置为 1 分钟：

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>如何向队列发送消息
若要向服务总线队列发送消息，应用程序需要对 **Azure::ServiceBusService** 对象调用 **send\_queue\_message()** 方法。 发往服务总线队列的消息以及从服务总线队列接收的消息是 **Azure::ServiceBus::BrokeredMessage** 对象，它们具有一组标准属性（如 **label** 和 **time\_to\_live**）、一个用于保存自定义的特定于应用程序的属性的字典和一段任意应用程序数据的正文。 应用程序可以通过将字符串值作为消息传送来设置消息正文，任何必需的标准属性将用默认值来填充。

下面的示例演示如何使用 **send\_queue\_message()** 向名为 "test-queue" 的队列发送一条测试消息：

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大为 64 KB，其中包括标准和自定义应用程序属性。 一个队列可包含的消息数不受限制，但消息的总大小受限。 此队列大小是在创建时定义的，上限为 5 GB。

## <a name="how-to-receive-messages-from-a-queue"></a>如何从队列接收消息
对 **Azure::ServiceBusService** 对象使用 **receive\_queue\_message()** 方法可从队列接收消息。 默认情况下，消息在被读取的同时会被锁定，从而无法从队列中删除。 但是，可以通过将 **:peek_lock** 选项设置为 **false**，在读取消息时将其从队列中删除。

默认行为使读取和删除变成一个两阶段操作，从而也有可能支持不允许遗漏消息的应用程序。 当 Service Bus 收到请求时，它会查找下一条要使用的消息，锁定该消息以防其他使用者接收，然后将该消息返回到应用程序。 应用程序处理完该消息（或将它可靠地存储起来留待将来处理）后，通过调用 **delete\_queue\_message()** 方法并提供要删除的消息作为参数来完成接收过程的第二阶段。 **delete\_queue\_message()** 方法将此消息标记为“已使用”并将其从队列中删除。

如果 **:peek\_lock** 参数设置为 **false**，读取并删除消息将是最简单的模式，并且最适合在发生故障时应用程序允许不处理消息的情况。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 因为服务总线会将消息标记为“已使用”，所以在应用程序重新启动并开始再次使用消息时，它会遗漏在崩溃之前使用过的消息。

以下示例演示如何使用 **receive\_queue\_message()** 接收和处理消息。 该示例先通过将 **:peek\_lock** 设置为 **false** 接收并删除一条消息，然后再接收另一条消息，最后使用 **delete\_queue\_message()** 删除该消息：

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何处理应用程序崩溃和不可读消息
Service Bus 提供了相关功能来帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则它可以对 **Azure::ServiceBusService** 对象调用 **unlock\_queue\_message()** 方法。 这将导致服务总线解锁队列中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

还存在与队列中已锁定消息关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），服务总线将自动解锁该消息并使它可再次被接收。

如果应用程序在处理消息之后，调用 **delete\_queue\_message()** 方法之前崩溃，则在应用程序重新启动时会将该消息重新传送给它。 此情况通常称作*至少处理一次*，即每条消息至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。 这通常可以通过使用消息的 **message\_id** 属性来实现，该属性在多次传送尝试中保持不变。

## <a name="next-steps"></a>后续步骤
现在，你已了解有关 Service Bus 队列的基础知识，单击下面的链接可了解更多信息。

* [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)的概述。
* 访问 GitHub 上的 [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) 存储库。

有关本文中讨论的 Azure 服务总线队列与[如何通过 Ruby 使用队列存储](../storage/storage-ruby-how-to-use-queue-storage.md)一文中讨论的 Azure 队列的比较，请参阅 [Azure 队列和 Azure 服务总线队列 - 比较与对照](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


