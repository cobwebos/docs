---
title: 教程：使用 Azure CLI 在 HDInsight 中创建启用 Apache Kafka REST 代理的群集
description: 了解如何在 Azure HDInsight 上使用 Kafka REST 代理执行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201704"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>教程：使用 Azure CLI 在 HDInsight 中创建启用 Apache Kafka REST 代理的群集

本教程介绍如何使用 Azure 命令行接口 (CLI) 在 Azure HDInsight 中创建启用 Apache Kafka [REST 代理](./rest-proxy.md)的群集。 Azure HDInsight 是适用于企业的分析服务，具有托管、全面且开源的特点。 Apache Kafka 是开源分布式流式处理平台。 通常用作消息代理，因为它可提供类似于发布-订阅消息队列的功能。 Kafka REST 代理可让你使用 HTTP 通过 [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) 来与 Kafka 群集交互。 Azure CLI 是用于管理 Azure 资源的 Microsoft 跨平台命令行体验。

仅可通过相同虚拟网络内的资源访问 Apache Kafka API。 可以使用 SSH 直接访问群集。 若要将其他服务、网络或虚拟机连接到 Apache Kafka，则必须首先创建虚拟机，然后才能在网络中创建资源。 有关详细信息，请参阅[使用虚拟网络连接到 Apache Kafka](./apache-kafka-connect-vpn-gateway.md)。

在本教程中，学习以下内容：

> [!div class="checklist"]
> * Kafka REST 代理的先决条件
> * 使用 Azure CLI 创建 Apache Kafka 群集

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 一个已注册到 Azure AD 的应用程序。 编写的用来与 Kafka REST 代理交互的客户端应用程序将使用此应用程序的 ID 和机密对 Azure 进行身份验证。 有关详细信息，请参阅[将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)。

* 一个 Azure AD 安全组，其中包含已注册的应用程序作为成员。 此安全组用于控制允许哪些应用程序与 REST 代理交互。 有关创建 Azure AD 组的详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

* Azure CLI。 确保至少安装版本 2.0.79。 请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-an-apache-kafka-cluster"></a>创建 Apache Kafka 群集

1. 登录到 Azure 订阅。

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. 设置环境变量。 本教程中的变量用法基于 Bash。 在其他环境中需要进行细微的更改。

    |变量 | 说明 |
    |---|---|
    |resourceGroupName|将 RESOURCEGROUPNAME 替换为新资源组的名称。|
    |location|将 LOCATION 替换为要在其中创建群集的区域。 如需有效位置的列表，请使用 `az account list-locations` 命令|
    |clusterName|将 CLUSTERNAME 替换为新群集的全局唯一名称。|
    |storageAccount|将 STORAGEACCOUNTNAME 替换为新存储帐户的名称。|
    |httpPassword|将 PASSWORD 替换为群集登录名 **admin** 的密码。|
    |sshPassword|将 PASSWORD 替换为安全外壳用户名 **sshuser** 的密码。|
    |securityGroupName|将 SECURITYGROUPNAME 替换为 Kafka REST 代理的客户端 AAD 安全组名称。 变量将传递给 `az-hdinsight-create` 的 `--kafka-client-group-name` 参数。|
    |securityGroupID|将 SECURITYGROUPID 替换为 Kafka REST 代理的客户端 AAD 安全组 ID。 变量将传递给 `az-hdinsight-create` 的 `--kafka-client-group-id` 参数。|
    |storageContainer|群集将使用的存储容器，对于本教程请保留现有值。 此变量将设置为群集的名称。|
    |workernodeCount|群集中的工作器节点数，对于本教程请保留现有值。 为了保证高可用性，Kafka 至少需要 3 个辅助角色节点|
    |clusterType|HDInsight 群集的类型，对于本教程请保留现有值。|
    |clusterVersion|HDInsight 群集版本，对于本教程请保留现有值。 Kafka REST 代理要求至少安装群集版本 4.0。|
    |componentVersion|Kafka 版本，对于本教程请保留现有值。 Kafka REST 代理要求至少安装组件版本 2.1。|

    使用所需的值更新变量。 然后，输入 CLI 命令来设置环境变量。

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. 输入以下命令[创建资源组](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)：

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. 输入以下命令[创建 Azure 存储帐户](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)：

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. 输入以下命令从 Azure 存储帐户中[提取主密钥](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)，然后将其存储在某个变量中：

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. 输入以下命令[创建 Azure 存储容器](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)：

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [创建 HDInsight 群集](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。 输入该命令之前，请注意以下参数：

    1. Kafka 群集所需的参数：

        |参数 | 说明|
        |---|---|
        |--type|值必须是 **Kafka**。|
        |--workernode-data-disks-per-node|每个工作器节点要使用的数据磁盘数。 HDInsight Kafka 仅支持数据磁盘。 本教程使用的值为 **2**。|

    1. Kafka REST 代理所需的参数：

        |参数 | 说明|
        |---|---|
        |--kafka-management-node-size|节点的大小。 本教程使用的值为 **Standard_D4_v2**。|
        |--kafka-client-group-id|Kafka REST 代理的客户端 AAD 安全组 ID。 该值是从变量 **$securityGroupID** 传递的。|
        |--kafka-client-group-name|Kafka REST 代理的客户端 AAD 安全组名称。 该值是从变量 **$securityGroupName** 传递的。|
        |--version|HDInsight 群集版本必须至少为 4.0。 该值是从变量 **$clusterVersion** 传递的。|
        |--component-version|Kafka 版本必须至少为 2.1。 该值是从变量 **$componentVersion** 传递的。|
    
        若要创建不含 REST 代理的群集，请在 `az hdinsight create` 命令中去除 `--kafka-management-node-size`、`--kafka-client-group-id` 和 `--kafka-client-group-name`。

    1. 如果有现有的虚拟网络，请添加参数 `--vnet-name` 和 `--subnet` 及其值。

    输入以下命令创建群集：

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    可能需要几分钟时间才能完成群集创建过程。 通常大约为 15 分钟。

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

输入以下命令中的全部或部分来删除资源：

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

使用 Azure CLI 在 Azure HDInsight 中成功创建启用 Apache Kafka REST 代理的群集后，接下来请使用 Python 代码来与 REST 代理交互：

> [!div class="nextstepaction"]
> [创建示例应用程序](./rest-proxy.md#client-application-sample)