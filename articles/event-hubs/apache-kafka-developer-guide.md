---
title: 阿帕奇卡夫卡活动中心开发人员指南
description: 本文提供了描述如何将 Kafka 应用程序与 Azure 事件中心集成的文章的链接。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: d90bf8efab28624672dcedb5bf53d45052dd9123
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605129"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure 活动中心 Apache Kafka 开发人员指南
本文提供了描述如何将 Apache Kafka 应用程序与 Azure 事件中心集成的文章的链接。 

## <a name="overview"></a>概述
事件中心提供 Kafka 终结点，现有的基于 Kafka 的应用程序可将该终结点用作运行你自己的 Kafka 群集的替代方案。 事件中心支持 Apache Kafka 协议 1.0 及更高版本，并且可与现有 Kafka 应用程序（包括 MirrorMaker）配合使用。 有关详细信息，请参阅[Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>快速入门
您可以在 GitHub 和此内容集中找到快速入门，帮助您快速启动 Kafka 的事件中心。

### <a name="quickstarts-in-github"></a>GitHub 中的快速入门
请参阅**azure 事件集线器 for kafka**回购中的以下快速启动： 

| 客户端语言/框架 | 说明 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>此快速入门将演示如何使用使用 .NET Core 2.0 用 C# 编写的示例生产者和使用者创建和连接到事件中心 Kafka 终结点。</p><p>此示例基于[康因的 Apache Kafka .NET 客户端](https://github.com/confluentinc/confluent-kafka-dotnet)，该客户端已修改为卡夫卡的事件中心。</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 此快速入门将演示如何使用 Java 编写的示例生成器和使用者创建和连接到事件中心 Kafka 终结点。 |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>此快速入门将演示如何使用 Node 中编写的示例生成器和使用者创建和连接到事件中心 Kafka 终结点。</p><p>此示例使用节点[rdkafka](https://github.com/Blizzard/node-rdkafka)库。 </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>此快速入门将演示如何使用用 python 编写的示例生成器和使用者创建和连接到事件中心 Kafka 终结点。</p><p>此示例基于[康因的阿帕奇卡夫卡Python客户端](https://github.com/confluentinc/confluent-kafka-python)，修改用于卡夫卡的事件中心。</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>此快速入门将演示如何使用 Go 编写的示例生成器和使用者创建和连接到事件中心 Kafka 终结点。</p><p>此示例基于[康因的阿帕奇卡夫卡戈朗客户端](https://github.com/confluentinc/confluent-kafka-go)，修改用于卡夫卡的事件中心。</p>| 
| [萨拉玛卡夫卡戈](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 此快速入门将演示如何使用使用[Sarama Kafka 客户端](https://github.com/Shopify/sarama)库在 Go 中编写的示例生产者和使用者创建和连接到事件中心 Kafka 终结点。 |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 此快速入门将演示如何使用与 Apache Kafka 分布捆绑的 CLI 创建和连接到事件中心 Kafka 终结点。| 
| [卡夫卡特](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat 是一种非 JVM 命令行消费者和生产商，基于利布卡夫卡，因其速度和占地面积小而广受欢迎。 此快速入门包含示例配置和几个简单示例 kakacat 命令。 | 
 
### <a name="quickstarts-in-docs"></a>DOCS 中的快速入门
请参阅快速入门：使用此内容集中的[Kafka 协议使用事件中心进行数据流，该协议](event-hubs-quickstart-kafka-enabled-event-hubs.md)提供有关如何流式传输到事件中心的分步说明。 您学习如何使用生产者和消费者与事件中心交谈，只需在应用程序中更改配置。 


## <a name="tutorials"></a>教程 

### <a name="tutorials-in-github"></a>GitHub 中的教程
请参阅 GitHub 上的以下教程：

| 教程 | 说明 | 
| ------------------------- | ----------- | 
| [阿卡](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 本教程演示如何在不更改协议客户端或运行自己的群集的情况下将 Akka 流连接到启用 Kafka 的事件中心。 有两个使用**Java**和**Scala**编程语言的单独教程。 | 
| [“连接”](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 本文档将引导您完成将 Kafka Connect 与 Azure 事件中心集成，以及部署基本文件流源和 FileStreamSink 连接器。 虽然这些连接器不用于生产，但它们演示了端到端 Kafka 连接方案，其中 Azure 事件中心伪装成 Kafka 代理。| 
| [文件](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 本文档将引导您通过 Filebeat 的 Kafka 输出集成 Filebeat 和事件中心。 | 
| [弗林克](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 本教程将演示如何在不更改协议客户端或运行自己的群集的情况下将 Apache Flink 连接到启用 Kafka 的事件中心。 | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 本文档将引导您使用 Fluentd 的`out_kafka`输出插件集成 Fluentd 和事件中心。 |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 本教程介绍如何使用不同的协议在使用者和生产者之间交换事件。 |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 本教程将引导您使用 Logstash Kafka 输入/输出插件将 Logstash 与启用卡夫卡的事件中心集成。 | 
| [镜像制造商](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 本教程演示如何通过镜像事件中心服务中的 Kafka 输入流，将现有的 Kafka 管道集成到 Azure 中。 |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 本教程将演示如何将 Apache NiFi 连接到事件中心命名空间。 | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 快速入门向您展示如何使用使用 Go 和 Java 编程语言编写的示例生产者和使用者创建和连接到事件中心 Kafka 终结点。 |
| [汇能的架构注册表](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 本教程将引导您完成 Kafka 的架构注册表和事件中心集成。 | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 本教程将演示如何在不更改协议客户端或运行自己的 Kafka 群集的情况下将 Spark 应用程序连接到事件中心。 | 

### <a name="tutorials-in-docs"></a>DOCS 中的教程
此外，请参阅教程：使用此内容集中[的流分析处理事件中心事件](event-hubs-kafka-stream-analytics.md)，其中演示如何将数据流式传输到事件中心并使用 Azure 流分析处理。

## <a name="how-to-guides"></a>操作指南
请参阅本文档中的以下操作指南：

| 项目 | 说明 | 
| ------- | ----------- | 
| [在事件中心镜像 Kafka 代理](event-hubs-kafka-mirror-maker-tutorial.md) | 演示如何使用卡夫卡镜像器在事件中心镜像 Kafka 代理。 |
| [将 Apache Spark 连接到事件中心](event-hubs-kafka-spark-tutorial.md) | 引导您完成将 Spark 应用程序连接到事件中心，进行实时流式传输。 |
| [将 Apache Flink 连接到事件中心](event-hubs-kafka-flink-tutorial.md) | 演示如何在不更改协议客户端或运行自己的群集的情况下将 Apache Flink 连接到事件中心。 |
| [将阿帕奇卡夫卡连接与事件中心（预览）](event-hubs-kafka-connect-tutorial.md) | 引导您完成将 Kafka Connect 与事件中心集成，并部署基本的 FileStreamSource 和 FileStreamSink 连接器。 |
| [将 Akka Streams 连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md) | 演示如何在不更改协议客户端或运行自己的群集的情况下将 Akka 流连接到事件中心。 |
| [使用具有 Azure 活动中心的 Apache Kafka 的弹簧启动器](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | 演示如何配置使用 Spring Boot 初始化器创建的基于 Java 的春云流绑定器，以便将 Apache Kafka 与 Azure 事件中心一起使用。 |

## <a name="next-steps"></a>后续步骤
在快速入门和教程文件夹中查看 GitHub 存储库[Azure 事件中心（用于 kafka）](https://github.com/Azure/azure-event-hubs-for-kafka)中的示例。

此外，请参阅以下文章：

- [事件中心的阿帕奇卡夫卡故障排除指南](apache-kafka-troubleshooting-guide.md)
- [常见问题 - 阿帕奇卡夫卡事件中心](apache-kafka-frequently-asked-questions.md)
- [事件中心的阿帕奇卡夫卡迁移指南](apache-kafka-migration-guide.md)



