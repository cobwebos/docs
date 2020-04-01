---
title: 将 Apache Kafka 工作负荷迁移到 Azure HDInsight 4.0
description: 了解如何将 HDInsight 3.6 上的 Apache Kafka 工作负荷迁移到 HDInsight 4.0。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437018"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>将 Apache Kafka 工作负荷迁移到 Azure HDInsight 4.0

Azure HDInsight 4.0 提供最新的开源组件，并在性能、连接和安全性方面有了很大的增强。 本文档介绍如何将 HDInsight 3.6 上的 Apache Kafka 工作负荷迁移到 HDInsight 4.0。 将工作负荷迁移到 HDInsight 4.0 后，可以使用 HDInsight 3.6 所不能提供的许多新功能。

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka 迁移路径

HDInsight 3.6 支持卡夫卡的两个版本：1.0.0 和 1.1.0。 HDInsight 4.0 支持版本 1.1.0 和 2.1.0。 根据要运行的 Kafka 版本和 HDInsight 版本，有多种受支持的迁移路径。 下文将介绍这些路径，下图也演示了这些路径。

* **在最新版本（推荐）上运行 Kafka 和 HDInsight：** 将 HDInsight 3.6 和 Kafka 1.0 或 1.1.0 应用程序迁移到 HDInsight 4.0，卡夫卡 2.1.0（路径 D 和 E 如下）。
* **在最新版本上运行 HDInsight，但卡夫卡仅在较新版本上运行 HDInsight：** 将 HDInsight 3.6 和卡夫卡 1.0.0 应用程序迁移到 HDInsight 4.0，卡夫卡 1.1.0（下面的路径 B）。
* **在最新版本上运行 HDInsight，保留卡夫卡版本**：将 HDInsight 3.6 和卡夫卡 1.1.0 应用程序迁移到 HDInsight 4.0，卡夫卡 1.1.0（路径 C 如下）。
* **在较新版本上运行卡夫卡，保留 HDInsight 版本**：将 Kafka 1.0.0 应用程序迁移到 1.1.0，并保留 HDInsight 3.6（下面的路径 A）。 请注意，此选项仍需部署新群集。 不支持升级现有群集上的 Kafka 版本。 使用所需的版本创建群集后，迁移 Kafka 客户端以使用新群集。

![HDInsight 3.6 上的 Apache Kafka 的升级路径](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka 版本

### <a name="kafka-110"></a>Kafka 1.1.0
  
从 Kafka 1.0.0 迁移到 1.1.0 后，可以利用以下新功能：

* Kafka 控制器的改进可以加速受控关机，使你能够更快地重启中介，以及在出现问题后进行恢复。 
* [FetchRequests 逻辑](https://issues.apache.org/jira/browse/KAFKA-6254)的改进使你能够在群集上创建更多的分区（从而获得更多的主题）。 
* Kafka Connect 支持主题的[记录标头](https://issues.apache.org/jira/browse/KAFKA-5142)和[正则表达式](https://issues.apache.org/jira/browse/KAFKA-3073)。 

有关完整更新列表，请参阅 [Apache Kafka 1.1 发行说明](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)。

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

迁移到 Kafka 2.1 后，可以利用以下功能：

* 中介复原能力随着复制协议的改进而得到提高。
* KafkaAdminClient API 中的新功能。
* 可配置的配额管理。
* 支持 Zstandard 压缩。

有关完整更新列表，请参阅 [Apache Kafka 2.0 发行说明](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html)和 [Apache Kafka 2.1 发行说明](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)。

## <a name="kafka-client-compatibility"></a>Kafka 客户端兼容性

新的 Kafka 中介支持旧版客户端。 [KIP-35 - 检索协议版本](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version)引入了一种动态确定 Kafka 代理和[KIP-97 功能的机制：改进的 Kafka 客户端 RPC 兼容性策略](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy)引入了新的兼容性策略和 Java 客户端的保证。 以前，Kafka 客户端必须与相同或更高版本的中介交互。 现在，更高版本的 Java 客户端以及支持 KIP-35 的其他客户端（例如 `librdkafka`）可以回退到较旧的请求类型，或者在功能不可用时引发相应的错误。

![升级 Kafka 客户端兼容性](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

请注意，这并不意味着客户端支持旧版中介。  有关详细信息，请参阅[兼容性矩阵](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)。

## <a name="general-migration-process"></a>一般迁移过程

以下迁移指导假设在单个虚拟网络中的 HDInsight 3.6 上部署了 Apache Kafka 1.0.0 或 1.1.0 群集。 现有中介包含一些主题，并正在由生成者和使用者使用。

![假设的当前 Kafka 环境](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

若要完成迁移，请执行以下步骤：

1. **部署新的 HDInsight 4.0 群集和客户端用于测试。** 部署新的 HDInsight 4.0 Kafka 群集。 如果可以选择多个 Kafka 群集版本，建议选择最新版本。 部署后，根据需要设置一些参数，并创建与现有环境同名的主题。 此外，根据需要设置 TLS 并自带密钥 （BYOK） 加密。 然后，检查此设置是否可在新群集上正常工作。

    ![部署新的 HDInsight 4.0 群集](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **切换生成者应用程序的群集，并等待所有队列数据已由当前使用者使用。** 新的 HDInsight 4.0 Kafka 群集准备就绪后，将现有生成者目标切换到新群集。 在现有使用者应用已使用现有群集中的所有数据之前，请将此目标保持原样。

    ![切换生成者应用的群集](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **切换使用者应用程序上的群集。** 确认现有使用者应用程序已用完现有群集中的所有数据后，将连接切换到新群集。

    ![切换使用者应用上的群集](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **根据需要删除旧群集并测试应用程序。** 完成切换并正常运行后，根据需要删除旧的 HDInsight 3.6 Kafka 群集，以及在测试中使用的生成者和使用者。

## <a name="next-steps"></a>后续步骤

* [Apache Kafka HDInsight 群集的性能优化](apache-kafka-performance-tuning.md)
* [快速入门：使用 Azure 门户在 Azure HDInsight 中创建 Apache Kafka 群集](apache-kafka-get-started.md)
