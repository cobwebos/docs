---
title: 连接 Apache Spark 应用 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何将 Apache Spark 与适用于 Kafka 的 Azure 事件中心配合使用。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 8535b7dc81da8c46685e12c3861793b919296cb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061574"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>将 Apache Spark 应用程序与 Azure 事件中心连接
本教程详细介绍如何将 Spark 应用程序连接到事件中心进行实时流式处理。 此集成可实现流式传输，无需更改你的协议客户端，也无需运行你自己的 Kafka 或 Zookeeper 群集。 本教程需要 Apache Spark 2.4 和 Apache Kafka v2.0 +。

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/) 上提供了此示例

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 创建事件中心命名空间
> * 克隆示例项目
> * 运行 Spark
> * 从用于 Kafka 的事件中心读取
> * 写入到用于 Kafka 的事件中心

## <a name="prerequisites"></a>先决条件

开始本教程前，请确保具备：
-   Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Spark-Kafka 适配器已更新，可以支持自 Spark v2.4 以来发布的 Kafka v2.0。 在以前版本的 Spark 中，此适配器支持 Kafka v0.10 及更高版本，但特别依赖于 Kafka v0.10 API。 由于适用于 Kafka 的事件中心不支持 Kafka v0.10，因此 v2.4 之前的 Spark 版本提供的 Spark-Kafka 适配器不受适用于 Kafka 生态系统的事件中心的支持。


## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
要从事件中心服务进行发送和接收，需要使用事件中心命名空间。 有关创建命名空间和事件中心的说明，请参阅[创建事件中心](event-hubs-create.md)。 获取事件中心连接字符串和完全限定域名 (FQDN) 供以后使用。 有关说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 

## <a name="clone-the-example-project"></a>克隆示例项目
克隆 Azure 事件中心存储库并导航到 `tutorials/spark` 子文件夹：

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>从用于 Kafka 的事件中心读取
进行一些配置更改以后，即可从适用于 Kafka 的事件中心读取数据。 根据命名空间提供的详细信息更新 **BOOTSTRAP_SERVERS** 和 **EH_SASL** 以后，即可使用事件中心进行流式处理，就像使用 Kafka 一样。 如需完整的示例代码，请查看 GitHub 上的 sparkConsumer.scala 文件。 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>写入到用于 Kafka 的事件中心
也可向事件中心写入数据，所用方式与向 Kafka 写入数据一样。 请勿忘记更新配置，也就是使用事件中心命名空间的信息更改 **BOOTSTRAP_SERVERS** 和 **EH_SASL**。  如需完整的示例代码，请查看 GitHub 上的 sparkProducer.scala 文件。 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [在事件中心镜像 Kafka 中转站](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Flink 连接到事件中心](event-hubs-kafka-flink-tutorial.md)
- [将 Kafka Connect 与事件中心集成](event-hubs-kafka-connect-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)
- [将 Akka Streams 连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)

