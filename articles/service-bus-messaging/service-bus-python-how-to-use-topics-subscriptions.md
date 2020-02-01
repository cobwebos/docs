---
title: 快速入门：通过 Python 使用 Azure 服务总线主题和订阅
description: 本文介绍如何创建 Azure 服务总线主题、订阅、向主题发送消息以及从订阅接收消息。
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 03e22c4c179850e5140015c0abc2d89f16d4b624
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774548"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>快速入门：通过 Python 使用服务总线主题和订阅

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文介绍如何将 Python 与服务总线主题和订阅配合使用。 示例使用 [Azure Python SDK][Azure Python package] 包执行以下操作： 

- 创建主题以及对主题的订阅
- 创建订阅筛选器和规则
- 将消息发送到主题 
- 从订阅接收消息
- 删除主题和订阅

## <a name="prerequisites"></a>必备条件
- Azure 订阅。 可以激活 [Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 遵循以下文章中的步骤创建的服务总线命名空间：[快速入门：使用 Azure 门户创建服务总线主题和订阅](service-bus-quickstart-topics-subscriptions-portal.md)。 复制“共享访问策略”屏幕中的命名空间名称、共享访问密钥名称和主密钥值，以便稍后在本快速入门中使用。  
- 装有 [Azure Python SDK][Azure Python package] 包的 Python 3.4x 或更高版本。 有关详细信息，请参阅 [Python 安装指南](/azure/python/python-sdk-azure-install)。

## <a name="create-a-servicebusservice-object"></a>创建 ServiceBusService 对象

可以通过某个 **ServiceBusService** 对象来使用主题以及对主题的订阅。 若要以编程方式访问服务总线，请将以下行添加到 Python 文件的顶部附近：

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

添加以下代码以创建 **ServiceBusService** 对象。 请将 `<namespace>`、`<sharedaccesskeyname>` 和 `<sharedaccesskeyvalue>` 替换为你的服务总线命名空间名称、共享访问签名 (SAS) 密钥名称和主密钥值。 可以在 [Azure 门户][Azure portal]上服务总线命名空间中的“共享访问策略”下找到这些值。 

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>创建主题

以下代码使用 `create_topic` 方法创建名为 `mytopic` 且采用默认设置的服务总线主题：

```python
bus_service.create_topic('mytopic')
```

可以使用主题选项来重写默认主题设置，例如消息生存时间 (TTL) 或最大主题大小。 以下示例创建名为 `mytopic` 的主题，其最大主题大小为 5 GB，默认消息 TTL 为 1 分钟：

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>创建订阅

也可以使用 **ServiceBusService** 对象创建对主题的订阅。 订阅可以包含筛选器，用于限制传送至其虚拟队列的消息集。 如果未指定筛选器，新订阅将使用默认的 **MatchAll** 筛选器，该筛选器会将发布到主题的所有消息置于订阅的虚拟队列中。 以下示例创建名为 `AllMessages` 的对 `mytopic` 的订阅，该订阅使用 **MatchAll** 筛选器：

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>对订阅使用筛选器

使用 **ServiceBusService** 对象的 `create_rule` 方法来筛选订阅中显示的消息。 可以在创建订阅时指定规则，或将规则添加到现有订阅。

最灵活的筛选器类型是 **SqlFilter**，它使用 SQL-92 的子集。 SQL 筛选器基于发布到主题的消息的属性运行。 有关可用于 SQL 筛选器的表达式的详细信息，请参阅 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 语法。

由于 **MatchAll** 默认筛选器会自动应用到所有新订阅，因此，必须先从要筛选的订阅中删除该筛选器，否则 **MatchAll** 会重写指定的任何其他筛选器。 可以使用 ServiceBusService 对象的 `delete_rule` 方法删除默认规则  。

以下示例创建名为 `HighMessages` 的对 `mytopic` 的订阅，该订阅使用名为 `HighMessageFilter`的 **SqlFilter** 规则。 `HighMessageFilter` 规则仅选择自定义 `messageposition` 属性大于 3 的消息：

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

以下示例创建名为 `LowMessages` 的对 `mytopic` 的订阅，该订阅使用名为 `LowMessageFilter`的 **SqlFilter** 规则。 `LowMessageFilter` 规则仅选择 `messageposition` 属性小于或等于 3 的消息：

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

`AllMessages`、`HighMessages` 和 `LowMessages` 都生效后，发送到 `mytopic` 的消息始终会传送到 `AllMessages` 订阅的接收方。 也可以根据消息的 `messageposition` 属性值，选择性地将消息传送到 `HighMessages` 或 `LowMessages` 订阅。 

## <a name="send-messages-to-a-topic"></a>将消息发送到主题

应用程序使用 **ServiceBusService** 对象的 `send_topic_message` 方法将消息发送到服务总线主题。

以下示例将 5 条测试消息发送到 `mytopic` 主题。 自定义 `messageposition` 属性值取决于循环的迭代，确定哪些订阅接收消息。 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>消息大小限制和配额

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大大小为 64 KB，其中包括标准和自定义应用程序属性。 主题中可以包含的消息数量不受限制，但主题包含的消息总大小有上限。 可以在创建时定义主题大小，上限为 5 GB。 

有关配额的详细信息，请参阅[服务总线配额][Service Bus quotas]。

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息

应用程序对 **ServiceBusService** 对象使用 `receive_subscription_message` 方法来从订阅接收消息。 以下示例从 `LowMessages` 订阅接收消息，并在读取消息后删除消息：

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`receive_subscription_message` 的 `peek_lock` 可选参数确定服务总线在从订阅读取消息后是否删除消息。 默认的消息接收模式是 *PeekLock* 或设置为 **True** 的 `peek_lock`，后者在读取（扫视）后锁定消息，而不会从订阅中删除消息。 然后，必须显式完成每个消息以将其从订阅中删除。

若要在从订阅读取消息后删除消息，可将 `peek_lock` 参数设置为 **False**，如前面的示例所示。 在执行接收操作过程中删除消息是最简单的模型，仅当应用程序在发生失败的情况下能够容许消息缺失时，该模型才能正常工作。 为了理解此行为，可以设想这样一种情形：应用程序发出接收请求，但在处理该请求之前发生崩溃。 如果在接收消息时它已被删除，当应用程序重启并重新开始使用消息时，它便缺少了在发生崩溃之前收到的消息。

如果应用程序不能容许消息缺失，则接收过程将变成由两个阶段组成的操作。 PeekLock 查找要使用的下一个消息，将其锁定以防其他使用方接收它，然后将该消息返回给应用程序。 处理或存储消息后，应用程序通过对 **Message** 对象调用 `complete` 方法完成接收过程的第二个阶段。  `complete` 方法会将消息标记为已使用，并将其从订阅中删除。

以下示例演示了 peek lock 方案：

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>处理应用程序崩溃和不可读消息

服务总线提供了相关功能，帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则可对 **Message** 对象调用 `unlock` 方法。 服务总线解锁订阅中的消息，并使其能够重新被同一个或另一个使用方应用程序接收。

订阅中锁定的消息还存在超时。 如果应用程序无法在锁定超时期满前处理消息（例如，如果应用程序崩溃），服务总线会自动解锁消息，让它再次可供接收。

如果应用程序在处理消息之后，但在调用 `complete` 方法之前崩溃，则在应用程序重启时会将该消息重新传送给它。 此行为通常称为“至少处理一次”。  每条消息将至少处理一次，但在某些情况下，可能会重新传送同一消息。 如果方案无法容许重复处理，可以使用消息的 **MessageId** 属性（多次尝试传送时，该属性保持不变）来处理重复消息传送。 

## <a name="delete-topics-and-subscriptions"></a>删除主题和订阅

若要删除主题和订阅，请使用 [Azure 门户][Azure portal]或 `delete_topic` 方法。 以下代码删除名为 `mytopic` 的主题：

```python
bus_service.delete_topic('mytopic')
```

删除主题会删除对该主题的所有订阅。 也可以单独删除订阅。 以下代码从 `mytopic` 主题删除名为 `HighMessages` 的订阅：

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

默认情况下，主题和订阅是持久性的，在删除之前会一直存在。 若要在特定的时间段过后自动删除订阅，可对订阅设置 [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) 参数。 

> [!TIP]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 可以使用服务总线资源管理器连接到服务总线命名空间并轻松管理消息传送实体。 该工具提供高级功能，例如导入/导出功能，以及用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤

了解有关服务总线主题的基础知识后，请单击以下链接获取更多信息：

* [队列、主题和订阅][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 参考

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
