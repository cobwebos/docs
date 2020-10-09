---
title: 创建 Azure HDInsight-Azure Data Lake Storage Gen2-Azure CLI
description: 了解如何通过 Azure CLI 将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: e8cfe55f4843439743535b978483d8518bd020fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858750"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>使用 Azure CLI 创建具有 Data Lake Storage Gen2 的群集

若要创建使用存储 Data Lake Storage Gen2 的 HDInsight 群集，请执行以下步骤。

## <a name="prerequisites"></a>先决条件

- 如果你不熟悉 Azure Data Lake Storage Gen2，请查看 [概述部分](hdinsight-hadoop-use-data-lake-storage-gen2.md)。 
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行 CLI 脚本示例，可以使用下列三种方法：
    - 在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)（见下一部分）。
    - 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
    - 如果喜欢使用本地 CLI 控制台，请[安装最新版 Azure CLI](/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 使用 `az login` 与要在其下部署用户分配的托管标识的 azure 订阅关联的帐户登录到 azure。Azure CLI。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

可以[下载示例模板文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)并[下载示例参数文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 在使用下面的模板和 Azure CLI 代码片段之前，请将以下占位符替换为其正确值：

| 占位符 | 说明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 订阅的 ID |
| `<RESOURCEGROUPNAME>` | 要在其中创建新群集和存储帐户的资源组。 |
| `<MANAGEDIDENTITYNAME>` | 将在具有 Azure Data Lake Storage Gen2 的存储帐户上获得权限的托管标识的名称。 |
| `<STORAGEACCOUNTNAME>` | 将创建 Azure Data Lake Storage Gen2 的新存储帐户。 |
| `<FILESYSTEMNAME>`  | 此群集应在存储帐户中使用的文件系统的名称。 |
| `<CLUSTERNAME>` | 你的 HDInsight 群集的名称。 |
| `<PASSWORD>` | 你选择的使用 SSH 及 Ambari 仪表板登录群集的密码。 |

以下代码片段将会执行下述初始步骤：

1. 登录到 Azure 帐户。
1. 设置要在其中执行创建操作的活动订阅。
1. 为新的部署活动创建新的资源组。
1. 创建用户分配的托管标识。
1. 将一个扩展添加到 Azure CLI，以使用 Data Lake Storage Gen2 的功能。
1. 使用标志 Data Lake Storage Gen2 创建新的存储帐户 `--hierarchical-namespace true` 。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下来，登录到门户。 将新的用户分配的托管标识添加到存储帐户上的“存储 Blob 数据参与者”角色。 此步骤在[使用 Azure 门户](hdinsight-hadoop-use-data-lake-storage-gen2.md)的步骤 3 中已描述。

 > [!IMPORTANT]
 > 请确保你的存储帐户具有用户分配的具有“存储 Blob 数据参与者”角色权限的标识，否则群集创建将失败。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

输入以下命令中的全部或部分来删除资源：

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>后续步骤

你已成功创建 HDInsight 群集。 现在可以了解如何使用群集了。

### <a name="apache-spark-clusters"></a>Apache Spark 群集

* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)
* [使用 Scala 创建独立的应用程序](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)