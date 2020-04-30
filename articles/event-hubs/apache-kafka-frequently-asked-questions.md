---
title: 常见问题解答-Azure 事件中心 Apache Kafka
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606738"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>常见问题-事件中心 Apache Kafka 
本文提供有关迁移到 Apache Kafka 的事件中心的一些常见问题的解答。

## <a name="do-you-run-apache-kafka"></a>是否运行 Apache Kafka？

不能。  我们针对事件中心基础结构执行 Kafka API 操作。  由于 Apache Kafka 与事件中心 AMQP 功能（即，生成、接收、管理等）之间存在紧密相关性，因此我们可以将事件中心的已知可靠性带入 Kafka PaaS 空间。

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>事件中心使用者组与 Kafka 使用者组
事件中心的事件中心使用者组与 Kafka 使用者组之间有何区别？ 事件中心的 Kafka 使用者组与标准事件中心使用者组完全不同。

**事件中心使用者组**

- 它们通过门户、SDK 或 Azure 资源管理器模板通过创建、检索、更新和删除（CRUD）操作进行管理。 事件中心使用者组不能为由。
- 它们是事件中心的子实体。 这意味着相同命名空间中的事件中心之间可以重复使用相同的使用者组名称，因为这些名称是单独的实体。
- 它们不用于存储偏移量。 使用外部偏移存储（例如 Azure 存储）完成协调 AMQP 消耗。

**Kafka 使用者组**

- 它们是由的。  Kafka 组可通过 Kafka 使用者组 Api 进行管理。
- 它们可以在事件中心服务中存储偏移量。
- 它们用作有效偏移键-值存储中的键。 对于`group.id`和`topic-partition`的唯一对，我们在 Azure 存储空间中存储偏移量（3倍复制）。 事件中心用户不会因存储 Kafka 偏移而产生额外的存储成本。 偏移量是通过 Kafka 使用者组 Api manipulable 的，但偏移存储*帐户*并不是直接显示，也不是事件中心用户的 manipulable。  
- 它们跨越一个命名空间。 在多个主题中对多个应用程序使用相同的 Kafka 组名称意味着，只要只有一个应用程序需要重新平衡，所有应用程序及其 Kafka 的客户端都将被重新平衡。  明智地选择组名称。
- 它们完全不同于事件中心使用者组。 **无**需使用 "$Default"，也无需担心干扰 AMQP 工作负荷的 Kafka 客户端。
- 它们不能在 Azure 门户中查看。 使用者组信息可通过 Kafka Api 进行访问。

## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [事件中心 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [事件中心 Apache Kafka 迁移指南](apache-kafka-migration-guide.md)
- [Apache Kafka 事件中心的故障排除指南](apache-kafka-troubleshooting-guide.md)
- [建议的配置](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

