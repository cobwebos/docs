---
title: 将 Apache Kafka 工作负荷迁移到 Azure HDInsight 4。0
description: 了解如何在 HDInsight 3.6 到 HDInsight 4.0 上迁移 Apache Kafka 工作负荷。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: d9ad5da27b4b5f8e4e447036c46613bad0f1f5c7
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563779"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>将 Apache Kafka 工作负荷迁移到 Azure HDInsight 4。0

Azure HDInsight 4.0 提供了最新的开源组件，在性能、连接性和安全性方面进行了重大改进。 本文档介绍如何将 HDInsight 3.6 上的 Apache Kafka 工作负荷迁移到 HDInsight 4.0。 将工作负荷迁移到 HDInsight 4.0 后，可以使用 HDInsight 3.6 上不可用的许多新功能。

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka 迁移路径

HDInsight 3.6 支持两个版本的 Kafka：1.0.0 和1.1.0。 HDInsight 4.0 支持1.1.0 和2.1.0 版本。 根据要运行的 Kafka 版本和 HDInsight 版本，有多个受支持的迁移路径。 下面将介绍这些路径，如下图所示。

* **在最新版本上运行 Kafka 和 HDInsight （推荐）** ：使用 Kafka 2.1.0 将 hdinsight 3.6 和 Kafka 1.0.0 或1.1.0 应用程序迁移到 hdinsight 4.0 （如下路径 D 和 E）。
* 在**最新版本上运行 HDInsight，但仅在最新版本上运行 Kafka**：使用 Kafka 1.1.0 将 hdinsight 3.6 和 Kafka 1.0.0 应用程序迁移到 hdinsight 4.0 （下面的路径 B）。
* **在最新版本上运行 HDInsight，保留 Kafka 版本**：使用 Kafka 1.1.0 将 hdinsight 3.6 和 Kafka 1.1.0 应用程序迁移到 hdinsight 4.0 （如下路径 C）。
* **在最新版本上运行 Kafka，保留 HDInsight 版本**：将 Kafka 1.0.0 应用程序迁移到1.1.0 并停留在 HDInsight 3.6 上（如下所示）。 请注意，此选项仍需要部署新群集。 不支持升级现有群集上的 Kafka 版本。 使用所需版本创建群集后，请迁移 Kafka 客户端以使用新群集。

![3\.6 Apache Kafka 的升级路径](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka 版本

### <a name="kafka-110"></a>Kafka 1.1。0
  
如果从 Kafka 1.0.0 迁移到1.1.0，可以利用以下新功能：

* Kafka 控制器的改进可以加快受控关闭，因此，你可以更快地重新启动代理并恢复问题。 
* [FetchRequests 逻辑](https://issues.apache.org/jira/browse/KAFKA-6254)中的改进，使你能够在群集上拥有更多分区（进而提供更多的主题）。 
* Kafka Connect 支持[记录标头](https://issues.apache.org/jira/browse/KAFKA-5142)和主题的[正则表达式](https://issues.apache.org/jira/browse/KAFKA-3073)。 

有关更新的完整列表，请参阅[Apache Kafka 1.1 发行说明](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)。

### <a name="apache-kafka-210"></a>Apache Kafka 2.1。0

如果迁移到 Kafka 2.1，可以利用以下功能：

* 由于改进了复制协议，因此更好的 broker 复原能力。
* KafkaAdminClient API 中的新功能。
* 可配置的配额管理。
* 支持 Zstandard 压缩。

有关更新的完整列表，请参阅[Apache Kafka 2.0 发行说明](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html)和[Apache Kafka 2.1 发行说明](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)。

## <a name="kafka-client-compatibility"></a>Kafka 客户端兼容性

新的 Kafka 代理支持较旧的客户端。 [K-35-检索协议版本](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version)引入了一种机制，用于动态确定 Kafka Broker 和[k 97 的功能：改进 KAFKA 客户端 RPC 兼容性策略](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy)引入了新的兼容性策略并保证了 Java 客户端。 以前，Kafka 客户端必须与同一版本或较新版本的 broker 交互。 现在，更高版本的 Java 客户端和其他支持 k 的客户端（如 `librdkafka`）可以回退到较旧35的请求类型，或者在功能不可用时引发相应的错误。

![升级 Kafka 客户端兼容性](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

请注意，这并不意味着客户端支持较旧的代理。  有关详细信息，请参阅[兼容性矩阵](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)。

## <a name="general-migration-process"></a>常规迁移过程

以下迁移指南假定在单个虚拟网络中的 HDInsight 3.6 上部署 Apache Kafka 1.0.0 或1.1.0 群集。 现有的代理具有一些主题，并正在由制造者和使用者主动使用。

![当前 Kafka 假设环境](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

若要完成迁移，请执行以下步骤：

1. **部署新的 HDInsight 4.0 群集和客户端进行测试。** 部署新的 HDInsight 4.0 Kafka 群集。 如果可以选择多个 Kafka 群集版本，建议选择最新版本。 部署之后，根据需要设置一些参数，并创建与现有环境相同的主题。 此外，根据需要设置 SSL 并自带密钥（BYOK）加密。 然后，使用新群集检查它是否正常工作。

    ![部署新的 HDInsight 4.0 群集](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **切换制造者应用程序的群集，并等待当前使用者使用所有队列数据。** 新的 HDInsight 4.0 Kafka 群集准备就绪后，将现有制造者目标切换到新群集。 将其保持原样，直到现有使用者应用使用现有群集中的所有数据。

    ![为生成者应用切换群集](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **切换使用者应用程序上的群集。** 确认现有使用者应用程序已完成使用现有群集中的所有数据后，请将连接切换到新群集。

    ![在使用者应用上切换群集](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **根据需要删除旧群集并测试应用程序。** 开关完成并正常工作后，请根据需要删除旧的 HDInsight 3.6 Kafka 群集以及在测试中使用的生成者和使用者。

## <a name="next-steps"></a>后续步骤

* [Apache Kafka HDInsight 群集的性能优化](apache-kafka-performance-tuning.md)
* [快速入门：在 Azure HDInsight 中使用 Azure 门户创建 Apache Kafka 群集](apache-kafka-get-started.md)