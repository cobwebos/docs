---
title: 从 Kafka 到 Azure Cosmos DB 的 Apache Spark 结构化流式处理 - Azure HDInsight | Microsoft Docs
description: 了解如何使用 Apache Spark 结构化流式处理从 Apache Kafka 读取数据，然后将数据存储到 Azure Cosmos DB 中。 本示例使用 Spark on HDInsight 中的 Jupyter 笔记本流式传输数据。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 63c536f1a8bdcfbbbd97b904f15ccf83043659e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>将 Spark 结构化流式处理与 Kafka 和 Azure Cosmos DB 配合使用

了解如何使用 Spark 结构化流式处理从 Apache Kafka on Azure HDInsight 读取数据，然后将数据存储到 Azure Cosmos DB 中。

Azure Cosmos DB 是一种全球分布式多模型数据库。 此示例使用 SQL API 数据库模型。 有关详细信息，请参阅[欢迎使用 Azure Cosmos DB](../cosmos-db/introduction.md) 文档。

Spark 结构化流式处理是建立在 Spark SQL 上的流处理引擎。 这允许以与批量计算相同的方式表达针对静态数据的流式计算。 有关结构化流式处理的详细信息，请参阅 Apache.org 上的 [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)（结构化流式处理编程指南 [Alpha]）。

> [!IMPORTANT]
> 此示例使用了 Spark 2.2 on HDInsight 3.6。
>
> 本文档中的步骤创建了一个包含 Spark on HDInsight 和 Kafka on HDInsight 群集的 Azure 资源组。 这些群集都位于 Azure 虚拟网络中，允许 Spark 群集直接与 Kafka 群集进行通信。
>
> 完成本文档中的步骤后，请记得删除这些群集，避免支付额外费用。

## <a name="create-the-clusters"></a>创建群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 中转站的权限。 与 Kafka 对话的任何内容都必须与 Kafka 群集中的节点位于同一 Azure 虚拟网络中。 对于此示例，Kafka 和 Spark 群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的 Spark 和 Kafka 群集图表](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 服务仅限于虚拟网络内的通信。 通过 Internet 可访问群集上的其他服务，例如 SSH 和 Ambari。 有关可用于 HDInsight 的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

尽管可手动创建 Azure 虚拟网络、Kafka 和 Spark 群集，但使用 Azure 资源管理器模板更简单。 使用以下步骤将 Azure 虚拟网络、Kafka 和 Spark 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，并在 Azure 门户中打开模板。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    Azure 资源管理器模板位于此项目的 GitHub 存储库中 ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb))。

    此模板可创建以下资源：

    * Kafka on HDInsight 3.6 群集。

    * Spark on HDInsight 3.6 群集。

    * 包含 HDInsight 群集的 Azure 虚拟网络。

        > [!NOTE]
        > 通过模板创建的虚拟网络使用 10.0.0.0/16 地址空间。

    * Azure Cosmos DB SQL API 数据库。

    > [!IMPORTANT]
    > 本示例使用的结构化流式处理笔记本需要 Spark on HDInsight 3.6。 如果使用早期版本的 Spark on HDInsight，则使用笔记本时会收到错误消息。

2. 使用以下信息填充“自定义部署”部分中的条目：
   
    ![HDInsight 自定义部署](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **订阅**：选择 Azure 订阅。
   
    * **资源组**：创建一个资源组或选择现有的资源组。 此组包含 HDInsight 群集。

    * **位置**：选择在地理上邻近的位置。

    * **Cosmos DB 帐户名**：此值用作 Cosmos DB 帐户的名称。

    * **基群集名称**：此值将用作 Spark 和 Kafka 群集的基名称。 例如，输入 myhdi 将创建名为 spark-myhdi 的 Spark 群集和名为 kafka-myhdi 的 Kafka 群集。

    * **群集版本**：HDInsight 群集版本。

        > [!IMPORTANT]
        > 此示例使用 HDInsight 3.6 进行测试，可能不适用于其他群集类型。

    * **群集登录用户名**：Spark 和 Kafka 群集的管理员用户名。

    * **群集登录密码**：Spark 和 Kafka 群集的管理员用户密码。

    * **SSH 用户名**：创建 Spark 和 Kafka 群集的 SSH 用户。

    * **SSH 密码**：Spark 和 Kafka 群集的 SSH 用户密码。

3. 阅读“条款和条件”，并选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，并选择“购买”。 创建群集大约需要 20 分钟时间。

> [!IMPORTANT]
> 创建群集、虚拟网络和 Cosmos DB 帐户最多可能需要 45 分钟时间。

## <a name="create-the-cosmos-db-database-and-collection"></a>创建 Cosmos DB 数据库和集合

本文档使用的项目在 Cosmos DB 中存储数据。 运行代码之前，必须首先在 Cosmos DB 实例中创建数据库和集合。 还必须检索文档终结点，以及用于对 Cosmos DB 的请求进行身份验证的密钥。 

可使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 执行此步骤。 以下脚本将创建名为 `kafkadata` 的数据库和名为 `kafkacollection` 的集合。 然后，将返回主键。

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

文档终结点和主键信息与以下文本类似：

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> 保存终结点和键值，以便用于 Jupyter 笔记本。

## <a name="get-the-kafka-brokers"></a>获取 Kafka 中转站

本示例中的代码连接到 Kafka 群集中的 Kafka 中转站主机。 若要查找两个 Kafka 中转站主机的地址，请使用以下 PowerShell 或 Bash 示例：

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]
> Bash 示例需要 `$CLUSTERNAME` 包含 Kafka 群集的名称。
>
> 本示例使用 [jq](https://stedolan.github.io/jq/) 实用工具来分析 JSON 文档外的数据。

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

出现提示时，输入群集登录（管理员）帐户的密码

输出与以下文本类似：

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

保存此信息，因为本文档的后面部分还将用到此信息。

## <a name="get-the-notebooks"></a>获取笔记本

可在 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) 处查看本文档所描述示例的代码。

## <a name="upload-the-notebooks"></a>上传笔记本

使用下列步骤，将项目中的笔记本上传到 Spark on HDInsight 群集：

1. 在 Web 浏览器中，连接到 Spark 群集上的 Jupyter 笔记本。 在下列 URL 中，将 `CLUSTERNAME` 替换为你的 __Spark__ 群集名：

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    出现提示时，输入创建群集时使用的群集登录名（管理员）和密码。

2. 在页面右上角，使用“上传”按钮将“Stream-taxi-data-to-kafka.ipynb”文件上传到群集。 选择“打开”开始上传。

3. 在笔记本列表中找到“Stream-taxi-data-to-kafka.ipynb”项，然后选择其旁边的“上传”按钮。

4. 重复步骤 1-3 加载 __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ 笔记本。

## <a name="load-taxi-data-into-kafka"></a>将出租车数据加载到 Kafka 中

上传文件后，选择“Stream-taxi-data-to-kafka.ipynb”项打开笔记本。 按照笔记本中的步骤将数据加载到 Kafka 中。

## <a name="process-taxi-data-using-spark-structured-streaming"></a>使用 Spark 结构化流式处理来处理出租车数据

在 Jupyter Notebook 主页上，选择“Stream-data-from-Kafka-to-Cosmos-DB.ipynb”项。 按照笔记本中的步骤使用 Spark 结构化流式处理将 Kafka 中的数据流式传输到 Azure Cosmos DB。

## <a name="next-steps"></a>后续步骤

现在你已了解如何使用 Spark 结构化流式处理，请参阅下列文档，深入了解如何使用 Spark、Kafka 和 Azure Cosmos DB：

* [如何将 Spark 流式处理 (DStream) 与 Kafka 配合使用](hdinsight-apache-spark-with-kafka.md)。
* [开始使用 Jupyter 笔记本和 Spark on HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [欢迎使用 Azure Cosmos DB](../cosmos-db/introduction.md)
