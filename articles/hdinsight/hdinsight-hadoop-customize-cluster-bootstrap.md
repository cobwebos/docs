---
title: 使用 bootstrap 自定义 Azure HDInsight 群集配置
description: 了解如何使用 .NET、PowerShell 和资源管理器模板以编程方式自定义 HDInsight 群集配置。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: eaddae04c198742007947831046139d80828534b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006579"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>使用 Bootstrap 自定义 HDInsight 群集

Bootstrap 脚本允许你以编程方式在 Azure HDInsight 中安装和配置组件。

在创建 HDInsight 群集时，有三种方式可用来设置配置文件设置：

* 使用 Azure PowerShell
* 使用 .NET SDK
* 使用 Azure Resource Manager 模板

例如，使用这些编程方法，你可以在以下文件中配置选项：

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties（kafka-broker 配置）

有关在创建时在 HDInsight 群集上安装其他组件的信息，请参阅[使用脚本操作自定义 HDInsight 群集 (Linux)](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="prerequisites"></a>先决条件

* 如果使用 PowerShell，需要安装 [Az 模块](https://docs.microsoft.com/powershell/azure/)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

以下 PowerShell 代码将自定义 [Apache Hive](https://hive.apache.org/) 配置：

> [!IMPORTANT]  
> 参数 `Spark2Defaults` 可能需要与 [Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) 一起使用。 你可以向参数传递空值，如以下代码示例中所示。

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

可在[附录](#appendix-powershell-sample)中找到完整的有效 PowerShell 脚本。

**若要验证更改，请执行以下操作：**

1. 导航至 `https://CLUSTERNAME.azurehdinsight.net/`，其中 `CLUSTERNAME` 是群集的名称。
1. 从左侧菜单中，导航到“Hive”**** > “配置”**** > “高级”****。
1. 展开“高级 hive-site”****。
1. 找到 **hive.metastore.client.socket.timeout** 并确认该值为 **90s**。

下面是有关自定义其他配置文件的更多示例：

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>使用 .NET SDK

请参阅[用于 .NET 的 Azure HDInsight SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板

可以在 Resource Manager 模板中使用 bootstrap：

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop 自定义群集启动 Azure 资源管理器模板](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

spark2-defaults 中用于切换配置的示例资源管理器模板代码片段会定期清除存储中的事件日志。  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>另请参阅

* [在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)提供了有关如何使用其他自定义选项创建 HDInsight 群集的说明。
* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 群集上安装并使用 Apache Spark](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [在 HDInsight 群集上安装并使用 Apache Giraph](hdinsight-hadoop-giraph-install.md)。

## <a name="appendix-powershell-sample"></a>附录：PowerShell 示例

此 PowerShell 脚本创建一个 HDInsight 群集并自定义 Hive 设置。 请确保为 `$nameToken`、`$httpPassword` 和 `$sshPassword` 输入值。

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
