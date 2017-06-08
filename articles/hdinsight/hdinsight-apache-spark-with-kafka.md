---
title: "Apache Spark 流式处理与 Kafka - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 HDInsight 上的 Apache Spark 读取数据并将数据写入 Apache Kafka on HDInsight。 此示例使用 Jupyter 笔记本中的 Scala 将数据写入到 Kafka on HDInsight，然后使用 Spark 流式处理读取它。"
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
ms.date: 05/15/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: ceff0df193b3356ed2a23f381ea65369063957b1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>将 Apache Spark 与 Kafka on HDInsight（预览版）配合使用

了解如何使用 Apache Spark 以流式方式将数据传入或传出 Apache Kafka。 在此文档中，了解如何使用 Spark on HDInsight 中的 Jupyter 笔记本以流式方式将数据传入或传出 Kafka。

> [!NOTE]
> 本文档中的步骤创建了一个包含 Spark on HDInsight 和 Kafka on HDInsight 群集的 Azure 资源组。 这些群集都位于 Azure 虚拟网络中，允许 Spark 群集直接与 Kafka 群集进行通信。
>
> 完成本文档中的步骤后，请记得删除这些群集，避免支付额外费用。

## <a name="create-the-clusters"></a>创建群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 中转站的权限。 与 Kafka 对话的任何内容都必须与 Kafka 群集中的节点位于同一 Azure 虚拟网络中。 对于此示例，Kafka 和 Spark 群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的 Spark 和 Kafka 群集图表](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> 虽然 Kafka 本身受限于虚拟网络中的通信，但可以通过 Internet 访问群集上的其他服务（例如 SSH 和 Ambari）。 有关可用于 HDInsight 的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

尽管可手动创建 Azure 虚拟网络、Kafka 和 Spark 群集，但使用 Azure Resource Manager 模板更简单。 使用以下步骤将 Azure 虚拟网络、Kafka 和 Spark 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，然后在 Azure 门户中打开模板。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 模板位于 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v2.json** 中。

2. 使用以下信息来填充“自定义部署”边栏选项卡上的项：
   
    ![HDInsight 自定义部署](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **资源组**：创建一个资源组或选择现有的资源组。 此组包含 HDInsight 群集。

    * **位置**：选择在地理上邻近的位置。

    * **基群集名称**：此值将用作 Spark 和 Kafka 群集的基名称。 例如，输入 **hdi** 创建名为 spark-hdi__ 的 Spark 群集和名为 **kafka-hdi** 的 Kafka 群集。

    * **群集登录用户名**：Spark 和 Kafka 群集的管理员用户名。

    * **群集登录密码**：Spark 和 Kafka 群集的管理员用户密码。

    * **SSH 用户名**：创建 Spark 和 Kafka 群集的 SSH 用户。

    * **SSH 密码**：Spark 和 Kafka 群集的 SSH 用户密码。

3. 阅读“条款和条件”，然后选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，然后选择“购买”。 创建群集大约需要 20 分钟时间。

创建资源后，会重定向到包含群集和 Web 仪表板的资源组边栏选项卡。

![虚拟网络和群集的“资源组”边栏选项卡](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 请注意，HDInsight 群集的名称为 **spark-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。

## <a name="get-the-code"></a>获取代码

本文档中描述的示例代码位于 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)。

## <a name="understand-the-code"></a>了解代码

此示例使用 Jupyter 笔记本中的 Scala 应用程序。 笔记本中的代码依赖于以下数据：

* __Kafka 中转站__：中转站过程在 Kafka 群集的每个 workernode 上运行。 生成者组件需要中转站列表，它可将数据写入 Kafka。

* __Zookeeper 主机__：从 Kafka 使用（读取）数据时，需使用 Kafka 群集的 Zookeeper 主机。

* __主题名称__：将数据写入其中和从中读取数据的主题的名称。 此示例需要名为 `sparktest` 的主题。

请参阅 [Kafka 主机信息](#kafkahosts)部分，了解如何获取 Kafka 中转站和 Zookeeper 主机的信息。

笔记本中的代码执行以下任务：

* 创建从名为 `sparktest` 的 Kafka 主题读取数据的使用者，然后计算数据中的每个字，并将单词和计数存储到名为 `wordcounts` 的临时表。

* 创建将随机语句写入名为 `sparktest` 的 Kafka 主题的生成者。

* 从 `wordcounts` 表选择数据来显示计数。

项目中的每个单元格包含注释或说明代码作用的文本部分。

## <a id="kafkahosts"></a>Kafka 主机信息

创建与 Kafka on HDInsight 配合使用的应用程序时，应首先获取 Kafka 中转站和 Kafka 群集的 Zookeeper 主机的信息。 客户端应用程序利用此操作与 Kafka 通信。

> [!NOTE]
> 不可通过 Internet 直接访问 Kafka 中转站和 Zookeeper 主机。 使用 Kafka 的任何应用程序必须在 Kafka 群集上运行，或在与 Kafka 群集相同的 Azure 虚拟网络中运行。 在这种情况下，该示例在同一虚拟网络中的 Spark on HDInsight 群集上运行。

从开发环境中使用以下命令来检索中转站和 Zookeeper 的信息：

* 获取 __Kafka 中转站__信息：

    ```bash
    curl -u admin:$PASSWORD -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'
    ```

    > [!NOTE]
    > 将 `$PASSWORD` 设置为在创建群集时使用的登录（管理员）密码。 将 `$CLUSTERNAME` 设置为在创建群集时使用的基名称。

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!NOTE]
    > 将 `$cluterName` 设置为 HDInsight 群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

* 获取 __Zookeeper 主机__信息：

    ```bash
    curl -u admin:$PASSWORD -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    ```

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

这两个命令均返回类似以下文本的信息：

* __Kafka 中转站__：`wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Zookeeper 主机__：`zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> 保存此信息，因为本文档中的几个步骤均会用到。

## <a name="use-the-jupyter-notebook"></a>使用 Jupyter 笔记本

若要使用示例 Jupyter 笔记本，必须将其上传到 Spark 群集上的 Jupyter 笔记本服务器中。 执行以下步骤上传此笔记本：

1. 在 Web 浏览器中，使用以下 URL 连接到 Spark 群集上的 Jupyter 笔记本服务器。 将 `CLUSTERNAME` 替换为 Spark 群集的名称。

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    出现提示时，输入创建群集时使用的群集登录名（管理员）和密码。

2. 在页面右上角，使用“上传”按钮上传 `KafkaStreaming.ipynb` 文件。 在文件浏览器对话框中，选择此文件并选择“打开”。

    ![使用“上传”按钮来选择并上传笔记本](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![选择 KafkaStreaming.ipynb 文件](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. 在笔记本列表中查找“KafkaStreaming.ipynb”项，然后选择其旁边的“上传”按钮。

    ![使用 KafkaStreaming.ipynb 项旁边的“上传”按钮，将其上传到笔记本服务器](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. 文件上传完成后，选择“KafkaStreaming.ipynb”项，打开该笔记本。 若要完成此示例，按照笔记本中的说明进行操作。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

由于本文档中的步骤在相同的 Azure 资源组中创建两个群集，因此可在 Azure 门户中删除资源组。 删除该组将删除按照本文档创建的所有资源、Azure 虚拟网络和群集使用的存储帐户。

## <a name="next-steps"></a>后续步骤

在本文档中，学习了如何使用 Spark 对 Kafka 进行读取和写入。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* [Apache Kafka on HDInsight 入门](hdinsight-apache-kafka-get-started.md)
* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](hdinsight-apache-kafka-mirroring.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](hdinsight-apache-storm-with-kafka.md)


