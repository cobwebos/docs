---
title: Azure 服务总线消息传送概述 | Microsoft Docs
description: 介绍服务总线消息传送
services: service-bus-messaging
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 05/22/2018
ms.custom: mvc
ms.author: sethm
ms.openlocfilehash: 0357602e6085b25fc6d11363113ebc962dc4d008
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643084"
---
# <a name="what-is-azure-service-bus"></a>什么是 Azure 服务总线？

Microsoft Azure 服务总线是一种完全托管的企业集成消息中转站。 服务总线最常用于将应用程序与服务彼此解耦，是一种用于异步数据和状态传输的可靠且安全的平台。 数据通过消息在不同的应用程序和服务之间传输。 消息采用二进制格式，可能包含 JSON、XML 或纯文本。 

一些常见的消息传送方案包括：

* 消息传送：传输业务数据，例如销售或采购订单、日志或库存变动。
* 解耦应用程序：提高应用程序和服务的可靠性和可伸缩性（客户端和服务不需同时联机）。
* 主题和订阅：启用发布者和订阅者之间的 1:*n* 关系。
* 消息会话：实现要求消息排序或消息延迟的工作流。

## <a name="namespaces"></a>命名空间

命名空间是一个适用于所有消息传送组件的作用域容器。 多个队列和主题可以位于一个命名空间中，命名空间通常用作应用程序容器。

## <a name="queues"></a>队列

消息可以发送到队列，也可以从其接收。 在能够使用接收应用程序接收并处理消息之前，可以通过队列来存储消息。

![队列](./media/service-bus-messaging-overview/about-service-bus-queue.png)

队列中的消息会排队，并在到达时加盖时间戳。 获得接受后，消息会安全地存储在冗余存储中。 消息以拉取模式传送，即按请求传送消息。

## <a name="topics"></a>主题

也可通过主题发送和接收消息。 队列通常用于点到点通信，而主题则用于发布/订阅方案。

![主题](./media/service-bus-messaging-overview/about-service-bus-topic.png)

主题可以有多个独立的订阅。 主题的订阅者可以收到发送到该主题的每个消息的副本。 订阅是命名实体，可以采用持久方式来创建，但是也可以过期或自动删除。

某些情况下，你可能不希望单个订阅接收发送给某个主题的所有消息。 如果是这样，则可使用[规则和筛选器](topic-filters.md)来定义触发可选[操作](topic-filters.md#actions)的条件，筛选指定的消息，并设置或修改消息属性。

## <a name="advanced-features"></a>高级功能

服务总线还有用于解决更复杂消息传送问题的高级功能。 以下部分介绍这些重要功能：

### <a name="message-sessions"></a>消息会话

若要在服务总线中实现先进先出 (FIFO) 保证，请使用会话。 使用[消息会话](message-sessions.md)，可以连贯有序的方式处理一系列无限多的相关消息。 

### <a name="auto-forwarding"></a>自动转发

[通过自动转发](service-bus-auto-forwarding.md)功能可将队列或订阅连接到作为相同命名空间组成部分的另一个队列或主题。 启用自动转发时，服务总线会自动删除放置在第一个队列或订阅（源）中的消息，并将其放入第二个队列或主题（目标）中。

### <a name="dead-lettering"></a>死信

服务总线支持使用[死信队列](service-bus-dead-letter-queues.md) (DLQ) 来存放无法传递给任何接收方的消息或无法处理的消息。 然后，可从 DLQ 中删除这些消息并对其进行检查。

### <a name="scheduled-delivery"></a>计划的传递

可以将消息提交到队列或主题以[供延迟处理](message-sequencing.md#scheduled-messages)；例如，将作业安排为在特定时间供系统处理。

### <a name="message-deferral"></a>消息延迟

当队列或订阅客户端收到一条它愿意处理，但由于应用程序中出现特殊状况而无法处理的消息时，该实体可[将消息的检索延迟](message-deferral.md)到将来的某个时间点。 该消息将保留在队列或订阅中，但会搁置处理。

### <a name="batching"></a>批处理

[客户端批处理](service-bus-performance-improvements.md#client-side-batching)允许队列或主题客户端延迟一段时间发送消息。 如果客户端在这段时间内发送其他消息，则会将这些消息以单个批次传送。 

### <a name="transactions"></a>事务

一个[事务](service-bus-transactions.md)将两个或更多操作组合成执行作用域。 服务总线支持对事务作用域内的消息传送实体（队列、主题、订阅）执行分组操作。

### <a name="filtering-and-actions"></a>筛选和操作

订阅者可以定义他们希望从主题接收的消息。 这些消息采用一个或多个[命名订阅规则](topic-filters.md)的形式指定。 对于每个匹配规则条件，订阅会生成消息的副本，这对于每个匹配规则可能会以不同方式进行批注。

### <a name="auto-delete-on-idle"></a>出现空闲队列时自动删除

可以使用[出现空闲队列时自动删除](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)功能指定一个空闲时间间隔，该时间间隔过后系统会自动删除队列。 最短持续时间为 5 分钟。

### <a name="duplicate-detection"></a>重复检测

如果出现错误，导致客户端怀疑某个发送操作的结果，则可使用[重复项检测](duplicate-detection.md)，此功能支持发送程序重新发送相同的消息，并让队列或主题放弃任何重复的副本，这样就消除了这些情况下的各种怀疑。

### <a name="sas-rbac-and-msi"></a>SAS、RBAC 和 MSI

服务总线支持多种安全协议，例如[共享访问签名](service-bus-sas.md) (SAS)、[基于角色的访问控制](service-bus-role-based-access-control.md) (RBAC)、[托管服务标识](service-bus-managed-service-identity.md) (MSI)。

### <a name="geo-disaster-recovery"></a>异地灾难恢复

在 Azure 区域数据中心遭遇停机的情况下，可以使用[异地灾难恢复](service-bus-geo-dr.md)在其他区域或数据中心进行数据处理，以实现连续运行。

### <a name="security"></a>“安全”

服务总线支持标准的 [AMQP 1.0](service-bus-amqp-overview.md) 和 [HTTP/REST](/rest/api/servicebus/) 协议。

## <a name="client-libraries"></a>客户端库

服务总线支持用于 [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master)、[Java](https://github.com/Azure/azure-service-bus-java/tree/master)、[JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client) 的客户端库。

## <a name="integration"></a>集成

服务总线与以下 Azure 服务完全集成：

- [事件网格](https://azure.microsoft.com/services/event-grid/) 
- [逻辑应用](https://azure.microsoft.com/services/logic-apps/) 
- [函数](https://azure.microsoft.com/services/functions/) 
- [Dynamics 365](https://dynamics.microsoft.com)
- [流分析](https://azure.microsoft.com/services/stream-analytics/)
 
## <a name="next-steps"></a>后续步骤

有关服务总线消息传送入门的内容，请参阅以下文章：

* [比较 Azure 消息传送服务](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* 详细了解 Azure 服务总线的[标准和高级](https://azure.microsoft.com/pricing/details/service-bus/)层及其定价
* [Azure 服务总线高级层的性能和延迟](https://blogs.msdn.microsoft.com/servicebus/2016/07/18/premium-messaging-how-fast-is-it/)
* 尝试 [.NET](service-bus-quickstart-powershell.md)、[Java](service-bus-quickstart-powershell.md) 或 [JMS](service-bus-quickstart-powershell.md) 中的快速入门
