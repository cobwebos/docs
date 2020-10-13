---
title: 常见问题解答 - 用于 Apache Kafka 的 Azure 事件中心
description: 本文解答了有关 Azure 事件中心的问题的常见问题，这些问题对不在其他地方的 Apache Kafka 客户端提供支持。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dc6a12b2098a1fdf33adda92b4347f91ab4e5489
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828116"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>常见问题解答 - 用于 Apache Kafka 的事件中心 
本文解答了有关迁移到用于 Apache Kafka 的事件中心的一些常见问题。

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Azure 事件中心是否在 Apache Kafka 上运行？

否。 Azure 事件中心是一个云本机多层代理，它支持由 Microsoft 开发和维护的多个协议，不使用任何 Apache Kafka 代码。 其中一个受支持的协议是 Kafka RPC 协议，适用于 Kafka 客户端的使用者和生成者 Api。 事件中心适用于许多现有的 Kafka 应用程序。 有关详细信息，请参阅 [Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)。 由于 Apache Kafka 和 Azure 事件中心的概念非常类似 (但) 不完全相同，因此我们可以为具有现有 Apache Kafka 投资的客户提供无与伦比的 Azure 事件中心可靠性。 

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>事件中心使用者组与Kafka 使用者组
事件中心使用者组与事件中心的 Kafka 使用者组之间有何区别？ 事件中心的 Kafka 使用者组与标准事件中心使用者组完全不同。

事件中心使用者组

- 它们通过门户、SDK 或 Azure 资源管理器模板使用创建、检索、更新和删除 (CRUD) 操作进行管理。 事件中心使用者组无法自动创建。
- 它们是事件中心的子实体。 这意味着同一命名空间中的事件中心之间可以重复使用相同的使用者组名称，因为这些名称是单独的实体。
- 它们不用于存储偏移量。 使用外部偏移存储（例如 Azure 存储）来协调 AMQP 消耗情况。

Kafka 使用者组

- 它们会自动创建。  Kafka 组可以通过 Kafka 使用者组 API 进行管理。
- 它们可以在事件中心服务中存储偏移量。
- 它们在实际上是偏移键值存储的存储中用作键。 如果 `group.id` 和 `topic-partition` 对是唯一的，我们会将偏移量存储在 Azure 存储中（3 倍复制）。 事件中心用户不会因存储 Kafka 偏移量而产生额外的存储成本。 偏移量可通过 Kafka 使用者组 API 进行操作，但是偏移量存储帐户对于事件中心用户来说并非直接可见或可操作的。  
- 它们跨越一个命名空间。 在多个主题中对多个应用程序使用相同的 Kafka 组名意味着，只要有一个应用程序需要重新均衡，所有应用程序及其 Kafka 客户端都会重新均衡。  请明智地选择组名。
- 它们完全不同于事件中心使用者组。 你不需要使用“$Default”，也不需要担心 Kafka 客户端会干扰 AMQP 工作负荷。
- 它们无法在 Azure 门户中查看。 使用者组信息可通过 Kafka API 进行访问。

## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [针对事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [针对事件中心的 Apache Kafka 迁移指南](apache-kafka-migration-guide.md)
- [针对事件中心的 Apache Kafka 故障排除指南](apache-kafka-troubleshooting-guide.md)
- [建议的配置](apache-kafka-configurations.md)

