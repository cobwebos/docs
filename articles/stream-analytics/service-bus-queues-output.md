---
title: 来自 Azure 流分析的服务总线队列输出
description: 本文介绍了作为 Azure 流分析的输出的服务总线队列。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: bad81e8929cd0c5c66c87fd9f6cc11dc746b3e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317765"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>来自 Azure 流分析的服务总线队列输出

[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)为一个或多个竞争使用方提供 FIFO 消息传递方式。 通常，接收方按照消息添加到队列中的临时顺序来接收并处理消息。 每条消息仅由一个消息使用者接收和处理。

在[兼容性级别 1.2](stream-analytics-compatibility-level.md) 中，Azure 流分析使用[高级消息队列协议 (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) 消息传递协议将内容写入服务总线队列和主题。 AMQP 让你能够使用开放标准协议构建跨平台的混合应用程序。

## <a name="output-configuration"></a>输出配置

下表列出了用于创建队列输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的易记名称，用于将查询输出定向到此服务总线队列。 |
| 服务总线命名空间 |包含一组消息实体的容器。 |
| 队列名称 |服务总线队列的名称。 |
| 队列策略名称 |创建队列时，还可以在“队列配置”选项卡上创建共享的访问策略。每个共享访问策略具有名称、所设权限以及访问密钥。 |
| 队列策略密钥 |用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 |
| 事件序列化格式 |输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 |对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| 分隔符 |仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 |仅适用于 JSON 类型。 分隔行指定通过新行分隔各个 JSON 对象，从而格式化输出。 如果选择“行分隔”，则读取 JSON 时，一次读取一个对象。 整个内容本身将不是有效的 JSON。 数组指定输出会被格式化为 JSON 对象的数组。 |
| 属性列 | 可选。 需要作为传出消息（而不是有效负载）的用户属性附加的以逗号分隔的列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |
| 系统属性列 | 可选。 需要附加到传出消息而不是附加到有效负载的系统属性和相应列名的键值对。  |

分区数[基于服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。

## <a name="partitioning"></a>分区

自动选择分区。 分区数基于[服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。 输出写入器的数量与输出队列中的分区数相同。

## <a name="output-batch-size"></a>输出批大小

对于标准层，最大消息大小为 256 KB，对于高级层，最大消息大小为1MB。 有关详细信息，请参阅 [服务总线限制](../service-bus-messaging/service-bus-quotas.md)。 若要优化，请对每条消息使用一个事件。

## <a name="custom-metadata-properties-for-output"></a>输出的自定义元数据属性

可将查询列作为用户属性附加到传出的消息。 这些列不会进入有效负载。 这些属性以字典形式在输出消息中提供。 键是列名，值是属性字典中的列值。  支持除“记录”和“数组”以外的其他所有流分析数据类型。

在下面的示例中，字段 `DeviceId` 和 `DeviceStatus` 添加到元数据。

1. 使用以下查询：

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. `DeviceId,DeviceStatus`在输出中将配置为属性列。

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="属性列":::

下图是使用 [服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)在 EventHub 中检查的预期输出消息属性。

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="属性列":::

## <a name="system-properties"></a>系统属性

可以将查询列作为[系统属性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true)附加到传出的服务总线队列或主题消息。

这些列不会进入有效负载，而是将查询列值填充到相应的 BrokeredMessage [系统属性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true)中。
支持这些系统属性 - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`。

这些列的字符串值将分析成相应的系统属性值类型，任何分析失败将被视为数据错误。
此字段以 JSON 对象格式提供。 此格式的详细信息如下所示：

* 用大括号 {} 括住。
* 以键值对的形式编写。
* 键和值必须是字符串。
* 键是系统属性名称，值是查询列名。
* 键和值以冒号分隔。
* 每个键/值对用逗号分隔。

下面展示了如何使用此属性 –

* 查询：`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 系统属性列：`{ "MessageId": "column1", "PartitionKey": "column2"}`

这会在服务总线队列消息的 `MessageId` 中设置 `column1` 的值，并在 PartitionKey 中设置 `column2` 的值。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
