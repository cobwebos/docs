---
title: 来自 Azure 流分析的事件中心输出
description: 本文介绍如何将数据从 Azure 流分析输出到 Azure 事件中心。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 86a6c1a15d804a6c758e90dbd4bdd7057a7a2716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295256"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>来自 Azure 流分析的事件中心输出

[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服务是具有高扩展性的发布 - 订阅事件引入器。 事件中心每秒可收集数百万个事件。 当流分析作业的输出成为另一个流式处理作业的输入时，可以将事件中心用作输出。 有关最大消息大小和批大小优化的信息，请参阅[输出批大小](#output-batch-size)部分。

## <a name="output-configuration"></a>输出配置

下表包含将事件中心中的数据流配置为输出所需的参数。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 | 查询中使用的易记名称，用于将查询输出定向到此事件中心。 |
| 事件中心命名空间 | 包含一组消息实体的容器。 创建新的事件中心后，还创建了事件中心命名空间。 |
| 事件中心名称 | 事件中心输出的名称。 |
| 事件中心策略名称 | 共享访问策略，可以在事件中心的“配置”选项卡上创建。每个共享访问策略具有名称、所设权限以及访问密钥。 |
| 事件中心策略密钥 | 用于对事件中心命名空间的访问权限进行身份验证的共享访问密钥。 |
| 分区键列 | 可选。 包含事件中心输出的分区键的列。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| 分隔符 | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 | 仅适用于 JSON 序列化。 分隔行指定通过新行分隔各个 JSON 对象，从而格式化输出。 如果选择“行分隔”，则读取 JSON 时，一次读取一个对象。 整个内容本身将不是有效的 JSON。 数组指定输出会被格式化为 JSON 对象的数组。  |
| 属性列 | 可选。 需要作为传出消息（而不是有效负载）的用户属性附加的以逗号分隔的列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |

## <a name="partitioning"></a>分区

分区根据分区对齐而有所不同。 如果事件中心输出的分区键与上游（上一个）查询步骤不相符，写入器的数量与事件中心输出中的分区数量相同。 每个写入器使用 [EventHubSender 类](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet&preserve-view=true)将事件发送到特定分区。 如果事件中心输出的分区键与上游（上一个）查询步骤不相符，写入器的数量与该上一步骤中的分区数量相同。 每个写入器使用 **EventHubClient** 中的 [SendBatchAsync 类](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet&preserve-view=true)将事件发送到所有输出分区。 

## <a name="output-batch-size"></a>输出批大小

最大消息大小为 256 KB，每条消息大小为 1 MB。 有关详细信息，请参阅 [事件中心限制](../event-hubs/event-hubs-quotas.md)。 如果输入/输出分区未对齐，则每个事件将单独打包成不超过最大消息大小的 `EventData`，并在批中发送。 如果使用[自定义元数据属性](#custom-metadata-properties-for-output)，也会发生这种情况。 如果输入/输出分区已对齐，则多个事件将打包成不超过最大消息大小的单个 `EventData` 实例，然后发送。

## <a name="custom-metadata-properties-for-output"></a>输出的自定义元数据属性

可将查询列作为用户属性附加到传出的消息。 这些列不会进入有效负载。 这些属性以字典形式在输出消息中提供。 键是列名，值是属性字典中的列值。  支持除“记录”和“数组”以外的其他所有流分析数据类型。

在下面的示例中，字段 `DeviceId` 和 `DeviceStatus` 添加到元数据。

1. 使用以下查询：

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. `DeviceId,DeviceStatus`在输出中将配置为属性列。

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="属性列":::

下图是使用 [服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)在 EventHub 中检查的预期输出消息属性。

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="属性列":::

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
