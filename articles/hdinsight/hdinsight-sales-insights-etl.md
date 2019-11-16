---
title: 教程：创建端到端的 ETL 管道用于派生销售见解
description: 了解如何在 Azure HDInsight 中创建 ETL 管道，以使用 Spark 按需群集和 Power BI 从销售数据中获取见解。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: d662ad59722658ed888aa732c1f45afdf48f850c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889208"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>教程：创建端到端的数据管道用于派生销售见解

在本教程中，你将生成一个端到端的数据管道用于执行提取、转换和加载 (ETL) 操作。 该管道将使用 Azure HDInsight 上运行的 Apache Spark 和 Apache Hive 群集来查询和处理数据。 你还将使用用于存储数据的 Azure Data Lake Storage Gen2，以及用于可视化的 Power BI 等技术。

此数据管道将合并各种存储中的数据，删除所有不需要的数据，追加新数据，并将所有这些数据加载回到存储中，以可视化业务见解。 请在[大规模提取、转换和加载 (ETL)](./hadoop/apache-hadoop-etl-at-scale.md) 中详细了解 ETL 管道。

![ETL 体系结构](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

下载 [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331)，以可视化本教程结束时生成的业务见解。

## <a name="create-resources"></a>创建资源

### <a name="clone-the-repository-with-scripts-and-data"></a>克隆包含脚本和数据的存储库

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单栏中打开 Azure Cloud Shell。 根据 Azure Cloud Shell 的提示选择自己的订阅来创建文件共享。

   ![打开 Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. 在“选择环境”下拉菜单中选择“Bash”。  
1. 登录到 Azure 帐户并设置订阅。 
1. 设置项目的资源组。
   1. 选择资源组的唯一名称。
   1. 在 Cloud Shell 中运行以下代码片段，以设置要在后续步骤中使用的变量：

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. 在 Cloud Shell 中输入以下命令，从 [HDInsight 销售见解 ETL 存储库](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)下载本教程所需的数据和脚本：

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. 在 shell 提示符下输入 `ls`，以查看创建的以下文件和目录：

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>部署管道所需的 Azure 资源 

1. 添加脚本 `chmod +x scripts/*.sh` 的执行权限。
1. 使用 `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` 命令运行该脚本，以在 Azure 中部署以下资源：

   1. Azure Blob 存储帐户。 此帐户保存公司销售数据。
   2. Azure Data Lake Storage Gen2 帐户。 此帐户充当两个 HDInsight 群集的存储帐户。 有关 HDInsight 和 Data Lake Storage Gen2 的详细信息，请参阅 [Azure HDInsight 与 Data Lake Storage Gen2 的集成](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)。
   3. 用户分配的托管标识。 此帐户为 HDInsight 群集授予对 Data Lake Storage Gen2 帐户的访问权限。
   4. Apache Spark 群集。 此群集用于清理和转换原始数据。
   5. Apache Hive 交互式查询群集。 此群集允许查询销售数据，并使用 Power BI 将其可视化。
   6. 网络安全组 (NSG) 规则支持的 Azure 虚拟网络。 此虚拟网络允许群集相互通信，并可保护其通信。 

创建群集的过程可能需要 20 分钟左右。

`resources.sh` 脚本包含以下命令。 此命令使用 Azure 资源管理器模板 (`resourcestemplate.json`) 创建采用所需配置的指定资源。

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

`resources.sh` 脚本还使用以下命令将销售数据 .csv 文件上传到新建的 Blob 存储帐户：

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

用于对群集进行 SSH 访问的默认密码为 `Thisisapassword1`。 若要更改密码，请转到 `resourcesparameters.json` 文件，并更改 `sparksshPassword`、`sparkClusterLoginPassword`、`llapClusterLoginPassword` 和 `llapsshPassword` 参数的密码。

### <a name="verify-deployment-and-collect-resource-information"></a>验证部署并收集资源信息

1. 若要检查部署状态，请在 Azure 门户上转到资源组。 选择“设置”下面的“部署”。   选择部署的名称 `ResourcesDeployment`。 在此处可以看到已成功部署的资源，以及仍在部署的资源。
1. 部署完成后，转到 Azure 门户并选择“资源组”>“<资源组名称>”。 
1. 找到为存储销售文件而创建的新 Azure 存储帐户。 该存储帐户的名称以 `blob` 开头，后面包含一个随机字符串。 请执行以下操作：
   1. 请记下该存储帐户名称供稍后使用。
   1. 选择 Blob 存储帐户的名称。
   1. 在门户左侧的“设置”下，选择“访问密钥”。  
   1. 复制“密钥 1”框中的字符串，并将其保存供稍后使用。 
1. 找到为 HDInsight 群集存储创建的 Data Lake Storage Gen2 帐户。 此帐户位于 Blob 存储帐户所在的同一资源组中，但以 `adlsgen2` 开头。 请执行以下操作：
   1. 请记下 Data Lake Storage Gen2 帐户的名称。
   1. 选择 Data Lake Storage Gen2 帐户的名称。
   1. 在门户左侧的“设置”下，选择“访问密钥”。  
   1. 复制“密钥 1”框中的字符串，并将其保存供稍后使用。 

> [!Note]
> 知道存储帐户的名称后，可以在 Azure Cloud Shell 提示符下使用以下命令获取帐户密钥：
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>创建数据工厂

Azure 数据工厂是一个有助于自动化 Azure Pipelines 的工具。 数据工厂并不是完成这些任务的唯一方式，但它是将过程自动化的极佳方式。 有关 Azure 数据工厂的详细信息，请参阅 [Azure 数据工厂文档](https://azure.microsoft.com/services/data-factory/)。 

此数据工厂包含一个管道，该管道包含两个活动： 

- 第一个活动将 Azure Blob 存储中的数据复制到 Data Lake Storage Gen2 存储帐户，以模拟数据引入。
- 第二个活动转换 Spark 群集中的数据。 脚本通过删除不需要的列来转换数据。 它还会追加一个新列来计算单笔交易产生的收入。

若要设置 Azure 数据工厂管道，请运行 `adf.sh` 脚本：

1. 使用 `chmod +x adf.sh` 添加文件执行权限。
1. 使用 `./adf.sh` 运行脚本。 

此脚本执行以下操作：

1. 创建对 Data Lake Storage Gen2 存储帐户拥有 `Storage Blob Data Contributor` 权限的服务主体。
1. 获取身份验证令牌，以授权对 [Data Lake Storage Gen2 文件系统 REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create) 的 POST 请求。
1. 在 `sparktransform.py` 和 `query.hql` 文件中填充 Data Lake Storage Gen2 存储帐户的实际名称。
1. 获取 Data Lake Storage Gen2 和 Blob 存储帐户的存储密钥。
1. 创建另一个资源部署，以创建 Azure 数据工厂管道及其关联的链接服务和活动。 它将存储密钥作为参数传递给模板文件，使链接服务能够正常访问存储帐户。

通过以下命令部署数据工厂管道：

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>运行数据管道

### <a name="trigger-the-data-factory-activities"></a>触发数据工厂活动

创建的数据工厂管道中的第一个活动将 Blob 存储中的数据移到 Data Lake Storage Gen2。 第二个活动对数据应用 Spark 转换，并将转换后的 csv 文件保存到新位置。 整个管道可能需要几分钟时间才能完成。

若要触发管道，可执行以下任一操作：

- 在 PowerShell 中运行以下命令来触发数据工厂管道： 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- 打开数据工厂并选择“创作和监视”。  触发复制管道，然后从门户触发 Spark 管道。 有关通过门户触发管道的信息，请参阅[使用 Azure 数据工厂在 HDInsight 中创建按需 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)。

若要验证管道是否已运行，可执行以下步骤之一：

- 通过门户转到数据工厂的“监视”部分。 
- 在 Azure 存储资源管理器中，转到你的 Data Lake Storage Gen2 存储帐户。 转到 `files` 文件系统，然后转到 `transformed` 文件夹，并检查其内容以查看管道运行是否成功。

有关使用 HDInsight 转换数据的其他方式，请参阅[这篇有关使用 Jupyter Notebook 的文章](/azure/hdinsight/spark/apache-spark-load-data-run-query)。

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>在交互式查询群集中创建一个表用于查看 Power BI 中的数据

1. 使用 SCP 将 `query.hql` 文件复制到 LLAP 群集：

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. 使用以下命令通过 SSH 访问 LLAP 群集，然后输入密码。 如果你未更改 `resourcesparameters.json` 文件，则密码为 `Thisisapassword1`。

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. 使用以下命令运行该脚本：

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

此脚本将在交互式查询群集中创建一个可从 Power BI 访问的托管表。 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>基于销售数据创建 Power BI 仪表板

1. 打开 Power BI Desktop。
1. 选择“获取数据”  。
1. 搜索“HDInsight 交互式查询群集”。 
1. 在此处粘贴群集的 URI。 它应采用格式 `https://<LLAP CLUSTER NAME>.azurehdinsight.net`。

   为数据库输入 `default`。
1. 输入用于访问群集的用户名和密码。

加载数据后，可以尝试创建仪表板。 若要开始使用 Power BI 仪表板，请参阅以下链接：

* [Power BI 设计器仪表板简介](https://docs.microsoft.com/power-bi/service-dashboards)
* [教程：Power BI 服务入门](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请通过以下命令删除所有资源，以免产生费用。

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [大规模提取、转换和加载 (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
