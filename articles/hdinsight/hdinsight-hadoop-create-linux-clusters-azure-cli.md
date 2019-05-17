---
title: 创建使用 Azure CLI-Azure HDInsight 的 Apache Hadoop 群集
description: 了解如何创建 HDInsight 群集使用跨平台 Azure CLI。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597674"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>使用 Azure CLI 创建 HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍了创建使用 Azure CLI 在 HDInsight 3.6 群集中的步骤。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

Azure CLI。 如果你尚未安装 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)的步骤。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>创建群集

1. 登录到你的 Azure 订阅。 如果你打算使用 Azure Cloud Shell，然后只需选择**试试**代码块右上角。 否则，请输入以下命令：

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 设置环境变量。 使用本文中的变量基于 Bash。 将需要其他环境的细微的变体。 请参阅[az hdinsight 创建](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)有关用于创建群集的可能参数的完整列表。

    |参数 | 描述 |
    |---|---|
    |`--size`| 在群集中的辅助角色节点数。 本文使用了变量`clusterSizeInNodes`作为值传递给`--size`。 |
    |`--version`| HDInsight 群集版本。 本文使用了变量`clusterVersion`作为值传递给`--version`。 另请参阅：[支持的 HDInsight 版本](./hdinsight-component-versioning.md#supported-hdinsight-versions)。|
    |`--type`| 如的 HDInsight 群集类型： hadoop、 interactivehive，hbase、 kafka、 storm、 spark、 rserver、 mlservices。  本文使用了变量`clusterType`作为值传递给`--type`。 另请参阅：[群集类型和配置](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。|
    |`--component-version`|中的空格分隔版本中的不同 Hadoop 组件版本组件 = 版本格式。 本文使用了变量`componentVersion`作为值传递给`--component-version`。 另请参阅：[Hadoop 组件](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。|

    替换`RESOURCEGROUPNAME`， `LOCATION`， `CLUSTERNAME`， `STORAGEACCOUNTNAME`，和`PASSWORD`与所需的值。 更改为所需的其他变量的值。 然后输入 CLI 命令。

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [创建资源组](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)通过输入以下命令：

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    有关有效位置的列表，使用`az account list-locations`命令，并使用从位置之一`name`值。

4. [创建 Azure 存储帐户](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)通过输入以下命令：

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [从 Azure 存储帐户中提取的主键](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)并将其存储在变量中，通过输入以下命令：

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [创建 Azure 存储容器](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)通过输入以下命令：

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [创建 HDInsight 群集](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)通过输入以下命令：

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight 群集具有各种不同的类型，与该群集进行优化的工作负荷或技术相对应。 没有任何方法支持创建组合多种类型的群集，如一个群集同时具有 Storm 和 HBase 类型。

    可能需要几分钟时间才能完成群集创建过程。 通常大约为 15 分钟。

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

输入全部或部分的以下命令删除资源：

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

使用 Azure CLI 成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集：

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 群集

* [为 Apache Storm on HDInsight 开发 Java 拓扑](storm/apache-storm-develop-java-topology.md)
* [在 Apache Storm on HDInsight 中使用 Python 组件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和监视拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)
