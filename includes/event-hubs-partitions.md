---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5d6d2285680884cf20daaeef0a1ade53afad5b90
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827647"
---
事件中心通过分区使用者模式提供消息流式处理功能，在此模式下，每个使用者只读取消息流的特定子集或分区。 此模式支持事件处理的水平缩放，同时提供队列和主题中不可用的其他面向流的功能。

分区是事件中心内保留的有序事件。 当较新的事件到达时，它们将添加到此序列的末尾。 可以将分区视为“提交日志”。

![事件中心](./media/event-hubs-partitions/partition.png)

事件中心按配置的保留时间保留数据，该时间适用于事件中心的所有分区。 事件根据特定的时间过期；无法显式删除事件。 由于分区互相独立且包含自身的数据序列，因此通常按不同速率增大。

![事件中心](./media/event-hubs-partitions/multiple_partitions.png)

分区数在创建时指定，必须介于 2 到 32 之间。 分区计数不可更改，因此在设置分区计数时应考虑长期规模。 分区是一种数据组织机制，与使用方应用程序中所需的下游并行度相关。 事件中心的分区数与预期会有的并发读取者数直接相关。 要将分区数增加到 32 以上，可以联系事件中心团队。

虽然可以标识分区并向其直接发送数据，但并不建议直接发送到分区， 相反，可以使用中引入的更高级构造[事件发布者](../articles/event-hubs/event-hubs-features.md#event-publishers)部分。 

分区中填充了一系列的事件数据，这些数据包含事件的正文、用户定义的属性包和元数据，例如，它在分区中的偏移量，以及它在流序列中的编号。

建议以 1:1 的比例来平衡吞吐量单位和分区数目，实现最佳缩放。 单个分区都有保证的入口和出口的最多为 1 个吞吐量单位。 虽然你也许可以在一个分区实现更高的吞吐量，但性能无法得到保证。 这就是我们强烈建议一个事件中心的分区数大于或等于吞吐量单位数的原因。

如果已确定所需总吞吐量，则可以知道所需吞吐量单位数和最小分区数，但应该有多少分区呢？ 选择的分区数取决于要实现的下游并行度以及未来的吞吐量需求。 我们不根据事件中心的分区数收费。

如需详细了解分区以及如何在可用性和可靠性之间进行取舍，请参阅[事件中心编程指南](../articles/event-hubs/event-hubs-programming-guide.md#partition-key)和[事件中心中的可用性和一致性](../articles/event-hubs/event-hubs-availability-and-consistency.md)这两篇文章。
