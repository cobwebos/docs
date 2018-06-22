---
title: 将 Kafka MirrorMaker 与用于 Kafka 生态系统的 Azure 事件中心配合使用 | Microsoft Docs
description: 在事件中心使用 Kafka MirrorMaker 镜像 Kafka 群集。
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 0693fc2fff5735fb2b3c0a9b8f1d3d256746f40d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298315"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>将 Kafka MirrorMaker 与用于 Kafka 生态系统的事件中心配合使用

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs) 上提供了此示例

新式云缩放应用的一个主要考虑因素是能够在不中断服务的情况下更新、改进和更改基础结构。 本教程介绍已启用 Kafka 的事件中心和 Kafka MirrorMaker 如何通过在事件中心服务中“镜像”Kafka 输入流将现有 Kafka 管道集成到 Azure 中。 

通过 Azure 事件中心 Kafka 终结点，用户可以使用 Kafka 协议（即 Kafka 客户端）连接到 Azure 事件中心。 通过对 Kafka 应用程序进行少量更改，可以连接到 Azure 事件中心并利用 Azure 生态系统的好处。 已启用 Kafka 的事件中心当前支持 Kafka 版本 1.0 及更高版本。

此示例演示如何使用 Kafka MirrorMaker 在已启用 Kafka 的事件中心镜像 Kafka 中转站。

   ![事件中心的 Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>先决条件

要完成本教程，请确保具有：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * 在 Ubuntu 上运行 `apt-get install default-jdk`，以便安装 JDK。
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](http://maven.apache.org/download.cgi)和[安装](http://maven.apache.org/install.html) Maven 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

要从事件中心服务进行发送和接收，需要使用事件中心命名空间。 请参阅[创建已启用 Kafka 的事件中心](event-hubs-create.md)，了解如何获取事件中心 Kafka 终结点。 请确保复制事件中心连接字符串，以供将来使用。

## <a name="clone-the-example-project"></a>克隆示例项目

获得已启用 Kafka 的事件中心连接字符串后，克隆 Azure 事件中心存储库并导航到 `mirror-maker` 子文件夹：

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>设置 Kafka 群集

使用 [Kafka 快速入门指南](https://kafka.apache.org/quickstart)设置具有所需设置的群集（或使用现有 Kafka 群集）。

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker 支持流“镜像”。 鉴于源和目标 Kafka 群集，MirrorMaker 可以确保发送到源群集的任何消息会由源和目标群集接收。 此示例演示如何使用已启用目标 Kafka 的事件中心镜像源 Kafka 群集。 此方案可用于从现有 Kafka 管道将数据发送到事件中心，而不会中断数据流。 

有关 Kafka MirrorMaker 的更多详细信息，请参阅 [Kafka 镜像/MirrorMaker 指南](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)。

### <a name="configuration"></a>配置

若要配置 Kafka MirrorMaker，为其提供一个 Kafka 群集作为其使用者/源和一个已启用 Kafka 的事件中心作为其生成者/目标。

#### <a name="consumer-configuration"></a>使用者配置

更新使用者配置文件 `source-kafka.config`，它可告知 MirrorMaker 源 Kafka 群集的属性。

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>生成者配置

现在更新生成者配置文件 `mirror-eventhub.config`，它可要求 MirrorMaker 将重复（或“已镜像”）数据发送到事件中心服务。 具体而言，更改 `bootstrap.servers` 和 `sasl.jaas.config` 以指向事件中心 Kafka 终结点。 事件中心服务要求安全的 (SASL) 通信，这可通过在以下配置中设置最后三个属性实现： 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>运行 MirrorMaker

使用新更新的配置文件从 Kafka 根目录中运行 Kafka MirrorMaker 脚本。 请务必将配置文件复制到 Kafka 根目录，或在下面的命令中更新其路径。

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

若要验证事件是否到达已启用 Kafka 的事件中心，请参阅 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)中的入口统计信息，或针对事件中心运行使用者。

运行 MirrorMaker 后，发送给源 Kafka 群集的任何事件由 Kafka 群集和镜像后的已启用 Kafka 的事件中心服务接收。 通过使用 MirrorMaker 和事件中心 Kafka 终结点，可以将现有的 Kafka 管道迁移到托管的 Azure 事件中心服务，而无需更改现有的群集或中断任何正在进行的数据流。

## <a name="next-steps"></a>后续步骤

* [了解事件中心](event-hubs-what-is-event-hubs.md)
* [了解用于 Kafka 生态系统的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
* 了解有关 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 的详细信息，以将事件从本地 Kafka 流式传输到云端已启用 Kafka 的事件中心。
* 了解如何使用[本机 Kafka 应用程序](event-hubs-quickstart-kafka-enabled-event-hubs.md)、[Apache Flink](event-hubs-kafka-flink-tutorial.md) 或 [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) 流式传输到已启用 Kafka 的事件中心。
