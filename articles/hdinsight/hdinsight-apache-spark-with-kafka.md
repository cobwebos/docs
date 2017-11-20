---
title: "Apache Spark 流式处理与 Kafka - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Spark Apache Spark 以流式方式 (DStream) 将数据传入或传出 Apache Kafka。 本示例使用 Spark on HDInsight 中的 Jupyter 笔记本流式传输数据。"
keywords: "kafka 示例, kafka zookeeper, spark 流式处理 kafka, spark 流式处理 kafka 示例"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 652719a80133434ecb31c00738dcda261cfa0683
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Kafka on HDInsight 的 Apache Spark 流式处理 (DStream) 示例

了解如何使用 Spark Apache Spark 以流式方式 (DStream) 将数据传入或传出 Apache Kafka on HDInsight。 本示例使用在 Spark 群集上运行的 Jupyter 笔记本。
> [!NOTE]
> 本文档中的步骤创建了一个包含 Spark on HDInsight 和 Kafka on HDInsight 群集的 Azure 资源组。 这些群集都位于 Azure 虚拟网络中，允许 Spark 群集直接与 Kafka 群集进行通信。
>
> 完成本文档中的步骤后，请记得删除这些群集，避免支付额外费用。

## <a name="create-the-clusters"></a>创建群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 中转站的权限。 与 Kafka 对话的任何内容都必须与 Kafka 群集中的节点位于同一 Azure 虚拟网络中。 对于此示例，Kafka 和 Spark 群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的 Spark 和 Kafka 群集图表](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> 虽然 Kafka 本身受限于虚拟网络中的通信，但可以通过 Internet 访问群集上的其他服务（例如 SSH 和 Ambari）。 有关可用于 HDInsight 的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

尽管可手动创建 Azure 虚拟网络、Kafka 和 Spark 群集，但使用 Azure 资源管理器模板更简单。 使用以下步骤将 Azure 虚拟网络、Kafka 和 Spark 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，并在 Azure 门户中打开模板。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure 资源管理器模板位于 https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json。

    > [!WARNING]
    > 若要确保 Kafka on HDInsight 的可用性，群集必须至少包含 3 个辅助节点。 此模板创建的 Kafka 群集包含三个辅助角色节点。

    此模板为 Kafka 和 Spark 创建 HDInsight 3.6 群集。

2. 使用以下信息填充“自定义部署”部分中的条目：
   
    ![HDInsight 自定义部署](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **资源组**：创建一个资源组或选择现有的资源组。 此组包含 HDInsight 群集。

    * **位置**：选择在地理上邻近的位置。

    * **基群集名称**：此值将用作 Spark 和 Kafka 群集的基名称。 例如，输入 **hdi** 将创建名为 __spark-hdi__ 的 Spark 群集和名为 **kafka-hdi** 的 Kafka 群集。

    * **群集登录用户名**：Spark 和 Kafka 群集的管理员用户名。

    * **群集登录密码**：Spark 和 Kafka 群集的管理员用户密码。

    * **SSH 用户名**：创建 Spark 和 Kafka 群集的 SSH 用户。

    * **SSH 密码**：Spark 和 Kafka 群集的 SSH 用户密码。

3. 阅读“条款和条件”，并选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，并选择“购买”。 创建群集大约需要 20 分钟时间。

创建资源后，会显示摘要页面。

![VNet 和群集的资源组摘要](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 请注意，HDInsight 群集的名称为 **spark-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。

## <a name="use-the-notebooks"></a>使用笔记本

本文档中描述的示例代码位于 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)。

要完成本示例，请按照 `README.md` 中的步骤进行操作。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

由于本文档中的步骤在相同的 Azure 资源组中创建两个群集，因此可在 Azure 门户中删除资源组。 删除该组将删除按照本文档创建的所有资源、Azure 虚拟网络和群集使用的存储帐户。

## <a name="next-steps"></a>后续步骤

在本示例中，了解如何使用 Spark 对 Kafka 进行读取和写入。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* [Apache Kafka on HDInsight 入门](kafka/apache-kafka-get-started.md)
* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](kafka/apache-kafka-mirroring.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](hdinsight-apache-storm-with-kafka.md)

