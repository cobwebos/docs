---
title: 来自 Azure 流分析的服务总线主题输出
description: 本文介绍作为 Azure 流分析的输出的服务总线主题。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: cc09912bb0c9ab553d180ff5cc06fc52c4c5cc0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261032"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>来自 Azure 流分析的服务总线主题输出

服务总线队列提供从发送方到接收方的一对一通信方法。 [服务总线主题](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供一对多形式的通信。

下表列出了用于创建服务总线主题输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的易记名称，用于将查询输出定向到此服务总线主题。 |
| 服务总线命名空间 |包含一组消息实体的容器。 创建新的事件中心后，还创建了 Service Bus 命名空间。 |
| 主题名称 |主题是消息传递实体，类似于事件中心和队列。 设计用于从设备和服务收集事件流。 在创建主题时，还会为其提供特定的名称。 发送到主题的消息在创建订阅后才会提供给用户，因此请确保主题下存在一个或多个订阅 |
| 主题策略名称 |创建服务总线主题时，还可以在主题的“配置”选项卡上创建共享的访问策略。每个共享访问策略具有名称、所设权限以及访问密钥。 |
| 主题策略密钥 |用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 |
| 事件序列化格式 |输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 |如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。 |
| 分隔符 |仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 属性列 | 可选。 需要作为传出消息（而不是有效负载）的用户属性附加的以逗号分隔的列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |
| 系统属性列 | 可选。 需要附加到传出消息而不是附加到有效负载的系统属性和相应列名的键值对。 |

分区数[基于服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。

## <a name="partitioning"></a>分区

自动选择分区。 分区数基于[服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。 输出写入器的数量与输出主题中的分区数相同。

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

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="属性列":::

下图是使用 [服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)在 EventHub 中检查的预期输出消息属性。

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="事件自定义属性":::

## <a name="system-properties"></a>系统属性

可以将查询列作为[系统属性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true)附加到传出的服务总线队列或主题消息。 这些列不会进入有效负载，而是将查询列值填充到相应的 BrokeredMessage [系统属性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true)中。
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
