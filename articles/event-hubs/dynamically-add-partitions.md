---
title: 将分区动态添加到 Azure 事件中心的某个事件中心
description: 本文介绍如何将分区动态添加到 Azure 事件中心的某个事件中心。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 4a729147eaa11497c66f82a9764dfee9492786b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87002533"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>动态将分区添加到 Azure 事件中心的事件中心（Apache Kafka 主题）
事件中心通过分区使用者模式提供消息流式处理功能，在此模式下，每个使用者只读取消息流的特定子集或分区。 此模式支持事件处理的水平缩放，同时提供队列和主题中不可用的其他面向流的功能。 分区是事件中心内保留的有序事件。 当较新的事件到达时，它们将添加到此序列的末尾。 有关一般分区的详细信息，请参阅[分区](event-hubs-scalability.md#partitions)

可在创建事件中心时指定分区数。 在某些情况下，可能需要在创建事件中心后添加分区。 本文介绍如何向现有事件中心动态添加分区。 

> [!IMPORTANT]
> 动态添加分区仅适用于专用事件中心群集。

> [!NOTE]
> 对于 Apache Kafka 客户端，事件中心映射到 Kafka 主题 。 有关 Azure 事件中心与 Apache Kafka 之间映射的更多信息，请参阅 [Kafka 和事件中心概念映射](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>更新分区计数
本部分说明如何以不同的方式（PowerShell、CLI 等）更新事件中心的分区计数。

### <a name="powershell"></a>PowerShell
使用 [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub?view=azurermps-6.13.0) PowerShell 命令更新事件中心中的分区。 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
使用 [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub?view=azure-cli-latest#az-eventhubs-eventhub-update) CLI 命令更新事件中心中的分区。 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>资源管理器模板
更新资源管理器模板中 `partitionCount` 属性的值，并重新部署模板以更新资源。 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
使用 `AlterTopics` API（例如，通过 kafka-topics CLI 工具）增加分区计数。 有关详细信息，请参阅[修改 Kafka 主题](http://kafka.apache.org/documentation/#basic_ops_modify_topic)。 

## <a name="event-hubs-clients"></a>事件中心客户端
让我们看看事件中心上的分区计数更新时，事件中心客户端的行为。 

向现有的事件中心添加分区后，事件中心客户端会从服务接收到 `MessagingException`，其作用是通知客户端：实体元数据（实体是事件中心，元数据是分区信息）已更改。 客户端将自动重新打开 AMQP 链接，然后该链接会选取已更改的元数据信息。 然后客户端正常运行。

### <a name="senderproducer-clients"></a>发送方/生成者客户端
事件中心提供三个发送方选项：

- **分区发送方** - 在此方案中，客户端直接向分区发送事件。 尽管分区是可识别的并且可以直接向其发送事件，但我们不建议采用这种模式。 添加分区不会影响此方案。 建议重启应用程序，以便其能够检测新添加的分区。 
- **分区密钥发送方** - 在此方案中，客户端使用密钥发送事件，以便属于该密钥的所有事件最终位于同一分区。 在这种情况下，服务将对密钥进行哈希处理，并路由到相应的分区。 由于哈希更改，分区计数更新可能导致出现乱序问题。 因此，如果在意排序，请在增加分区计数之前确保应用程序使用现有分区中的所有事件。
- **轮循机制发送方（默认）** - 在此方案中，事件中心服务以轮询方式在分区间发送事件。 事件中心服务可感知分区计数更改，并在分区计数更改后的几秒钟内发送到新的分区。

### <a name="receiverconsumer-clients"></a>接收方/使用者客户端
事件中心提供直接接收方和称为[事件处理器主机（旧 SDK）](event-hubs-event-processor-host.md)或[事件处理器（新 SDK）](event-processor-balance-partition-load.md)的简单使用者库。

- **直接接收方** - 直接接收方侦听特定分区。 当为事件中心横向扩展分区时，该接收方的运行时行为不受影响。 使用直接接收方的应用程序需要负责选择新分区并相应地分配接收方。
- **事件处理器主机** - 此客户端不会自动刷新实体元数据。 因此，它不会感知分区计数的增加。 重新创建事件处理器实例将导致提取实体元数据，进而会为新添加的分区创建新的 Blob。 预先存在的 Blob 不会受到影响。 建议重新启动所有事件处理器实例，以确保所有实例都能感知新添加的分区，并在使用者之间正确处理和分配负载平衡。

    如果使用旧版本的 .NET SDK ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/))，则当检查点中的分区计数与从服务中提取的分区计数不匹配时，事件处理器主机会在重新启动时删除现有的检查点。 此行为可能会影响你的应用程序。 

## <a name="apache-kafka-clients"></a>Apache Kafka 客户端
本部分介绍在更新事件中心的分区计数时，使用 Azure 事件中心的 Kafka 终结点的 Apache Kafka 客户端的行为。 

将 Apache Kafka 协议与事件中心配合使用的 Kafka 客户端与使用 AMQP 协议的事件中心客户端的行为有所不同。 Kafka 客户端每 `metadata.max.age.ms` 毫秒更新一次其元数据。 在客户端配置中指定此值。 `librdkafka` 库也使用相同的配置。 元数据更新会就服务更改通知客户端，包括分区计数增加。 有关配置的列表，请参阅[事件中心的 Apache Kafka 配置](apache-kafka-configurations.md)。

### <a name="senderproducer-clients"></a>发送方/生成者客户端
生成者始终规定发送请求包含每组生产记录的分区目标。 因此，所有生成分区操作都是在客户端上使用生成者的中转站元数据视图完成的。 在将新分区添加到生成者的元数据视图后，它们将可用于生成者请求。

### <a name="consumerreceiver-clients"></a>使用者/接收方客户端
当使用者组成员执行元数据刷新并选取新创建的分区时，该成员将启动组重新均衡。 然后，将为所有组成员刷新使用者元数据，并且新的分区将按分配的重新均衡列表来分配。

## <a name="recommendations"></a>建议

- 如果将分区密钥与生成者应用程序一起使用，并且依赖密钥哈希来确保分区中的排序，则不建议动态添加分区。 

    > [!IMPORTANT]
    > 虽然现有数据保持了排序状态，但由于添加了分区，分区计数更改，因此在这之后哈希的消息的分区哈希将中断。
- 在以下情况下，建议将分区添加到现有主题或事件中心实例：
    - 使用轮循机制（默认）的方法来发送事件时
     - Kafka 默认分区策略，例如“粘滞分配器”策略


## <a name="next-steps"></a>后续步骤
有关分区的详细信息，请参阅[分区](event-hubs-scalability.md#partitions)。

