---
title: "使用 PowerShell 创建 Azure HDInsight (Hadoop) | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 在 HDInsight 中的 Linux 上创建 Hadoop、HBase、Storm 或 Spark 群集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 44e418e52fc18dd22820331f7d921e789da62832
ms.lasthandoff: 03/25/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell 是强大的脚本环境，可以用于在 Microsoft Azure 中控制和自动执行工作负荷的部署和管理。 本文档介绍如何使用 Azure PowerShell 创建基于 Linux 的 HDInsight 群集。 此外，还提供了示例脚本。

> [!NOTE]
> Azure PowerShell 仅在 Windows 客户端上可用。 如果使用的是 Linux、Unix 或 Mac OS X 客户端，请参阅[使用 Azure CLI 创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-azure-cli.md)，了解如何使用 Azure CLI 创建群集。
> 
> 

## <a name="prerequisites"></a>先决条件
开始执行此过程之前请做好以下准备：

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell。
    有关将 Azure PowerShell 与 HDInsight 配合使用的详细信息，请参阅[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。 有关 HDInsight Windows PowerShell cmdlet 的列表，请参阅 [HDInsight cmdlet 参考](https://msdn.microsoft.com/library/azure/dn858087.aspx)。
  
    > [!IMPORTANT]
    > 使用 Azure Service Manager 管理 HDInsight 资源的 Azure PowerShell 支持**已弃用**，已在 2017 年 1 月 1 日删除。 本文档中的步骤使用的是与 Azure Resource Manager 兼容的新 HDInsight cmdlet。
    > 
    > 请按照 [安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 中的步骤安装最新版本的 Azure PowerShell。 如果你的脚本需要修改后才能使用与 Azure Resource Manager 兼容的新 cmdlet，请参阅 [迁移到适用于 HDInsight 群集的基于 Azure Resource Manager 的开发工具](hdinsight-hadoop-development-using-azure-resource-manager.md) ，了解详细信息。
    > 
    > 

### <a name="access-control-requirements"></a>访问控制要求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>创建群集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要使用 Azure PowerShell 创建 HDInsight 群集，必须完成以下过程：

* 创建 Azure 资源组
* 创建 Azure 存储帐户
* 创建 Azure Blob 容器
* 创建 HDInsight 群集

创建 Linux 群集必须设置的两个最重要参数是用于指定 OS 类型和 SSH 用户详细信息的参数：

* 确保将 **-OSType** 参数指定为 **Linux**。
* 若要在群集上对远程会话使用 SSH，可以指定 SSH 用户密码或 SSH 公钥。 如果你同时指定 SSH 用户密码和 SSH 公钥，则将忽略该密钥。 如果你要对远程会话使用 SSH 密钥，则必须在出现提示时指定空 SSH 密码。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

以下脚本演示了如何创建新群集：

    $token ="<SpecifyAnUniqueString>"
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

为 **$clusterCredentials** 指定的值用于创建群集的 Hadoop 用户帐户。 使用此帐户连接到群集。

为 **$sshCredentials** 指定的值用于创建群集的 SSH 用户。 使用此帐户在群集上启动远程 SSH 会话和运行作业。

> [!IMPORTANT]
> 在此脚本中，必须指定群集中要包含的工作节点数。 如果计划使用 32 个以上的辅助角色节点（在创建群集时配置或者是在创建之后通过扩展群集来配置），则还必须指定至少具有 8 个核心和 14 GB RAM 的头节点大小。
> 
> 有关节点大小和相关费用的详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。
> 
> 

创建群集可能需要 20 分钟。

下面的示例演示了如何添加其他存储帐户：

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>自定义群集
* 请参阅[使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
* 请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="delete-the-cluster"></a>删除群集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>后续步骤
成功创建 HDInsight 群集后，请通过以下资源了解如何使用群集。

### <a name="hadoop-clusters"></a>Hadoop 群集
* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 群集
* [HBase on HDInsight 入门](hdinsight-hbase-tutorial-get-started-linux.md)
* [为 HBase on HDInsight 开发 Java 应用程序](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 群集
* [为 Storm on HDInsight 开发 Java 拓扑](hdinsight-storm-develop-java-topology.md)
* [在 Storm on HDInsight 中使用 Python 组件](hdinsight-storm-develop-python-topology.md)
* [使用 Storm on HDInsight 部署和监视拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark 群集
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)


