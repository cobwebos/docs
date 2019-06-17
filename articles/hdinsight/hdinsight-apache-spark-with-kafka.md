---
title: Apache Spark 流式处理与 Apache Kafka - Azure HDInsight
description: 了解如何使用 Apache Spark 通过 DStreams 将数据流式传入或流式传出 Apache Kafka。 本示例使用 Spark on HDInsight 中的 Jupyter 笔记本流式传输数据。
keywords: kafka 示例, kafka zookeeper, spark 流式处理 kafka, spark 流式处理 kafka 示例
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e0c39ae5f5c23ae0715ef1eee38b6dd34704538a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690965"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Kafka on HDInsight 的 Apache Spark 流式处理 (DStream) 示例

了解如何使用 [Apache Spark](https://spark.apache.org/) 通过 [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html) 将数据流式传入或流式传出 [Apache Kafka](https://kafka.apache.org/) on HDInsight。 本示例使用在 Spark 群集上运行的 [Jupyter Notebook](https://jupyter.org/)。

> [!NOTE]  
> 本文档中的步骤创建了一个包含 Spark on HDInsight 和 Kafka on HDInsight 群集的 Azure 资源组。 这些群集都位于 Azure 虚拟网络中，允许 Spark 群集直接与 Kafka 群集进行通信。
>
> 完成本文档中的步骤后，请记得删除这些群集，避免支付额外费用。

> [!IMPORTANT]  
> 此示例使用 DStreams，这是较旧的 Spark 流式处理技术。 有关使用较新的 Spark 流式处理功能的示例，请参阅[使用 Apache Kafka 的 Spark 结构化流式处理](hdinsight-apache-kafka-spark-structured-streaming.md)文档。

## <a name="create-the-clusters"></a>创建群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 中转站的权限。 若要与 Kafka 通信，必须与 Kafka 群集中的节点在同一 Azure 虚拟网络中。 对于此示例，Kafka 和 Spark 群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的 Spark 和 Kafka 群集的关系图](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> 虽然 Kafka 本身受限于虚拟网络中的通信，但可以通过 Internet 访问群集上的其他服务（例如 SSH 和 Ambari）。 有关 HDInsight 所提供的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

尽管可手动创建 Azure 虚拟网络、Kafka 和 Spark 群集，但使用 Azure 资源管理器模板更简单。 使用以下步骤将 Azure 虚拟网络、Kafka 和 Spark 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，并在 Azure 门户中打开模板。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure 资源管理器模板位于 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** 。

    > [!WARNING]  
    > 若要确保 Kafka on HDInsight 的可用性，群集必须至少包含 3 个辅助节点。 此模板创建的 Kafka 群集包含三个工作节点。

    此模板为 Kafka 和 Spark 创建 HDInsight 3.6 群集。

2. 使用以下信息填充“自定义部署”部分中的条目  ：
   
    ![HDInsight 自定义部署](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **资源组**：创建一个组或选择有个现有的组。 此组包含 HDInsight 群集。

    * **位置**：选择在地理上邻近的位置。

    * **基群集名称**：此值将用作 Spark 和 Kafka 群集的基名称。 例如，输入 **hdistreaming** 将创建名为 __spark-hdistreaming__ 的 Spark 群集和名为 **kafka-hdistreaming** 的 Kafka 群集。

    * **群集登录用户名**：Spark 和 Kafka 群集的管理员用户名。

    * **群集登录密码**：Spark 和 Kafka 群集的管理员用户密码。

    * **SSH 用户名**：创建 Spark 和 Kafka 群集的 SSH 用户。

    * **SSH 密码**：Spark 和 Kafka 群集的 SSH 用户的密码。

3. 阅读“条款和条件”  ，并选择“我同意上述条款和条件”  。

4. 最后，选择“购买”  。 创建群集大约需要 20 分钟时间。

创建资源后，会显示摘要页面。

![VNet 和群集的资源组摘要](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]  
> 请注意，HDInsight 群集的名称为 **spark-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。

## <a name="use-the-notebooks"></a>使用笔记本

可在 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) 处查看本文档所描述示例的代码。

要完成本示例，请按照 `README.md` 中的步骤进行操作。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

由于本文档中的步骤在相同的 Azure 资源组中创建两个群集，因此可在 Azure 门户中删除资源组。 删除该组将删除按照本文档创建的所有资源、Azure 虚拟网络和群集使用的存储帐户。

## <a name="next-steps"></a>后续步骤

在本示例中，了解如何使用 Spark 对 Kafka 进行读取和写入。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* [Apache Kafka on HDInsight 入门](kafka/apache-kafka-get-started.md)
* [使用 MirrorMaker 创建 Apache Kafka on HDInsight 的副本](kafka/apache-kafka-mirroring.md)
* [将 Apache Storm 与 Apache Kafka on HDInsight 配合使用](hdinsight-apache-storm-with-kafka.md)

