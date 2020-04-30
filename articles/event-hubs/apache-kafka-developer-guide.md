---
title: 事件中心 Apache Kafka 开发人员指南
description: 本文提供相关文章的链接，这些文章介绍了如何将 Kafka 应用程序与 Azure 事件中心集成。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: b4d9dc51451a06b2c87ddce78547655b51c6fabd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729627"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure 事件中心 Apache Kafka 开发人员指南
本文提供了介绍如何将 Apache Kafka 应用程序与 Azure 事件中心集成的文章的链接。 

## <a name="overview"></a>概述
事件中心提供 Kafka 终结点，现有的基于 Kafka 的应用程序可将该终结点用作运行你自己的 Kafka 群集的替代方案。 事件中心支持 Apache Kafka 协议 1.0 及更高版本，并且可与现有 Kafka 应用程序（包括 MirrorMaker）配合使用。 有关详细信息，请参阅[Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>快速入门
你可以在 GitHub 和此内容集中找到快速入门，这有助于你快速提升 Kafka 的事件中心。

### <a name="quickstarts-in-github"></a>GitHub 中的快速入门
请参阅**kafka**存储库中的以下快速入门教程： 

| 客户端语言/框架 | 说明 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>本快速入门介绍如何使用 .NET Core 2.0，通过使用 c # 编写的示例制造者和使用者来创建和连接到事件中心 Kafka 终结点。</p><p>此示例基于[Confluent Apache Kafka 的 .net 客户端](https://github.com/confluentinc/confluent-kafka-dotnet)，经过修改后可用于 Kafka 的事件中心。</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 本快速入门介绍如何使用以 Java 编写的示例创建者和使用者来创建和连接到事件中心 Kafka 终结点。 |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>本快速入门教程将演示如何使用示例创建者和使用者编写的使用者来连接到事件中心 Kafka 终结点。</p><p>此示例使用[rdkafka](https://github.com/Blizzard/node-rdkafka)库。 </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>本快速入门介绍如何使用 python 中编写的示例创建者和使用者创建并连接到事件中心 Kafka 终结点。</p><p>此示例基于[Confluent 的 Apache Kafka Python 客户端](https://github.com/confluentinc/confluent-kafka-python)，经过修改后可用于 Kafka 的事件中心。</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>本快速入门介绍如何使用编写的示例创建者和使用者来创建和连接到事件中心 Kafka 终结点。</p><p>此示例基于[Confluent 的 Apache Kafka Golang 客户端](https://github.com/confluentinc/confluent-kafka-go)，经过修改后可用于 Kafka 的事件中心。</p>| 
| [Sarama kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 本快速入门教程将演示如何使用[Sarama Kafka 客户端](https://github.com/Shopify/sarama)库通过使用编写的示例创建者和使用者来创建和连接到事件中心 Kafka 终结点。 |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 本快速入门介绍如何使用与 Apache Kafka 分发捆绑在一起的 CLI 创建和连接到事件中心 Kafka 终结点。| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat 是一个非 JVM 命令行使用者，并基于 librdkafka，因为它的速度和较小的占用量。 本快速入门教程包含一个示例配置和几个简单的示例 kafkacat 命令。 | 
 
### <a name="quickstarts-in-docs"></a>文档中的快速入门
请参阅本内容集中的快速入门：[使用 Kafka 协议通过事件中心流式传输数据](event-hubs-quickstart-kafka-enabled-event-hubs.md)，其中提供了有关如何流式传输到事件中心的分步说明。 你将了解如何只需更改应用程序配置，即可使用生产者和使用者与事件中心通信。 


## <a name="tutorials"></a>教程 

### <a name="tutorials-in-github"></a>GitHub 中的教程
请参阅 GitHub 上的以下教程：

| 教程 | 说明 | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 本教程介绍如何将 Akka 流连接到已启用 Kafka 的事件中心，而无需更改协议客户端或运行自己的群集。 有两个单独的教程使用**Java**和**Scala**编程语言。 | 
| [“连接”](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 本文档将指导你将 Kafka Connect 与 Azure 事件中心集成，并部署基本 FileStreamSource 和 FileStreamSink 连接器。 尽管这些连接器并不用于生产用途，但它们演示了一个端到端的 Kafka 连接方案，其中 Azure 事件中心 masquerades 为 Kafka broker。| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 本文档将指导你通过 Filebeat 的 Kafka 输出来集成 Filebeat 和事件中心。 | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 本教程将演示如何将 Apache Flink 连接到启用 Kafka 的事件中心，而无需更改协议客户端或运行自己的群集。 | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 本文档将指导你使用 Fluentd 的`out_kafka`输出插件来集成 Fluentd 和事件中心。 |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 本教程介绍如何使用不同的协议在使用者和制造商之间交换事件。 |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 本教程将指导你使用 Logstash Kafka 输入/输出插件将 Logstash 与启用了 Kafka 的事件中心集成。 | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 本教程介绍事件中心和 Kafka MirrorMaker 如何通过在事件中心服务中镜像 Kafka 输入流，将现有 Kafka 管道集成到 Azure 中。 |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 本教程将演示如何将 Apache NiFi 连接到事件中心命名空间。 | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 快速入门介绍了如何使用以 to 和 Java 编程语言编写的示例制造者和使用者来创建和连接到事件中心 Kafka 终结点。 |
| [Confluent 的架构注册表](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 本教程将指导你将架构注册表和事件中心与 Kafka 集成。 | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 本教程将演示如何将 Spark 应用程序连接到事件中心，而无需更改协议客户端或运行自己的 Kafka 群集。 | 

### <a name="tutorials-in-docs"></a>文档教程
另请参阅教程：在此内容集中[使用流分析处理 Apache Kafka 事件中心事件](event-hubs-kafka-stream-analytics.md)，其中显示了如何使用 Azure 流分析将数据流式传输到事件中心并对其进行处理。

## <a name="how-to-guides"></a>操作指南
请参阅文档中的以下操作方法指南：

| 项目 | 说明 | 
| ------- | ----------- | 
| [在事件中心镜像 Kafka 代理](event-hubs-kafka-mirror-maker-tutorial.md) | 演示如何使用 Kafka MirrorMaker 镜像事件中心中的 Kafka broker。 |
| [将 Apache Spark 连接到事件中心](event-hubs-kafka-spark-tutorial.md) | 逐步讲解如何将 Spark 应用程序连接到事件中心以进行实时流式处理。 |
| [将 Apache Flink 连接到事件中心](event-hubs-kafka-flink-tutorial.md) | 说明如何将 Apache Flink 连接到事件中心，而无需更改协议客户端或运行自己的群集。 |
| [将 Apache Kafka 连接与事件中心集成（预览版）](event-hubs-kafka-connect-tutorial.md) | 介绍如何将 Kafka Connect 与事件中心集成并部署基本 FileStreamSource 和 FileStreamSink 连接器。 |
| [将 Akka Streams 连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md) | 说明如何将 Akka 流连接到事件中心，而无需更改协议客户端或运行自己的群集。 |
| [使用带有 Azure 事件中心的 Apache Kafka 的弹簧 Boot Starter](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | 演示如何配置使用春季 Boot 初始值设定项创建的基于 Java 的弹簧 Cloud Stream 联编程序，以将 Apache Kafka 与 Azure 事件中心一起使用。 |

## <a name="next-steps"></a>后续步骤
查看 GitHub 存储库中的示例 kafka 快速入门和教程文件夹下的 " [azure 事件中心](https://github.com/Azure/azure-event-hubs-for-kafka)"。

另请参阅以下文章：

- [Apache Kafka 事件中心的故障排除指南](apache-kafka-troubleshooting-guide.md)
- [常见问题-事件中心 Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [事件中心 Apache Kafka 迁移指南](apache-kafka-migration-guide.md)



