---
title: 常见问题 - 阿帕奇卡夫卡的 Azure 事件中心
description: 本文介绍使用不同协议（AMQP、Apache Kafka 和 HTTPS）的使用者和生成者在使用 Azure 事件中心时如何交换事件。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606738"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>常见问题 - 阿帕奇卡夫卡事件中心 
本文提供了一些常见问题的解答，这些常见问题是迁移到 Apache Kafka 的事件中心。

## <a name="do-you-run-apache-kafka"></a>你经营阿帕奇卡夫卡吗？

不是。  我们针对事件中心基础结构执行 Kafka API 操作。  由于 Apache Kafka 和事件中心 AMQP 功能（即生成、接收、管理等）之间存在紧密关联，因此我们能够将事件中心的已知可靠性引入 Kafka PaaS 空间。

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>事件中心使用者组与卡夫卡使用者组
事件中心使用者组和事件中心上的 Kafka 使用者组之间的区别是什么？ 事件中心的 Kafka 使用者组与标准事件中心使用者组完全不同。

**事件中心使用者组**

- 通过门户、SDK 或 Azure 资源管理器模板创建、检索、更新和删除 （CRUD） 操作管理它们。 无法自动创建事件中心使用者组。
- 它们是事件中心的子实体。 这意味着同一使用者组名称可以在同一命名空间中的事件中心之间重复使用，因为它们是单独的实体。
- 它们不用于存储偏移量。 协调的 AMQP 消耗使用外部偏移存储（例如 Azure 存储）完成。

**卡夫卡消费群体**

- 它们是自动创建的。  卡夫卡组可以通过卡夫卡消费群体 API 进行管理。
- 它们可以在事件中心服务中存储偏移量。
- 它们用作有效偏移键值存储中的键。 对于唯一的`group.id`和`topic-partition`，我们在 Azure 存储（3 倍复制）中存储偏移量。 事件中心用户不会因存储 Kafka 偏移而产生额外的存储成本。 偏移可以通过 Kafka 使用者组 API 进行操纵，但偏移存储*帐户*对于事件中心用户来说不是直接可见或可操作的。  
- 它们跨越一个命名空间。 对多个主题上的多个应用程序使用相同的 Kafka 组名称意味着，只要单个应用程序需要重新平衡，所有应用程序及其 Kafka 客户端都将重新平衡。  明智地选择群组名称。
- 它们与事件中心使用者组完全不同。 您**无需**使用"$Default"，也无需担心 Kafka 客户端干扰 AMQP 工作负载。
- 它们无法在 Azure 门户中查看。 可通过 Kafka API 访问消费者组信息。

## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [阿帕奇卡夫卡活动中心开发人员指南](apache-kafka-developer-guide.md)
- [事件中心的阿帕奇卡夫卡迁移指南](apache-kafka-migration-guide.md)
- [事件中心的阿帕奇卡夫卡故障排除指南](apache-kafka-troubleshooting-guide.md)
- [推荐的配置](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

