---
title: 使用 PowerShell 管理 HDInsight 中的 Apache Hadoop 群集 - Azure
description: 了解如何使用 Azure PowerShell 针对 HDInsight 中的 Apache Hadoop 群集执行管理任务。
services: hdinsight
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tylerfox
ms.openlocfilehash: 20b232c53427c8ce13ded2cd722a74b1a686b536
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360418"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 HDInsight 中的 Apache Hadoop 群集
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 可用于在 Azure 中控制和自动执行工作负荷的部署和管理。 本文介绍了如何使用 Azure PowerShell 管理 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 群集。 有关 HDInsight PowerShell cmdlet 的列表，请参阅 [HDInsight cmdlet 参考](https://msdn.microsoft.com/library/azure/dn479228.aspx)。

**先决条件**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在开始阅读本文前，必须具有以下项目：

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

如果已安装 Azure PowerShell 版本 0.9x，则必须先卸载该版本，再安装新版本。

若要检查所安装的 PowerShell 版本，请执行以下操作：

```powershell
Get-Module *Az*
```

若要卸载旧版本，请运行控制面板中的“程序和功能”。

## <a name="create-clusters"></a>创建群集
请参阅[使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>列出群集
使用以下命令可列出当前订阅中的所有群集：

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>显示群集
使用以下命令可显示当前订阅中特定群集的详细信息：

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>删除群集
使用以下命令来删除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

还可通过删除包含该群集的资源组删除群集。 删除资源群将删除组中的所有资源（包括默认存储帐户）。

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>缩放群集
使用群集缩放功能，可更改 Azure HDInsight 中运行的群集使用的辅助节点数，而无需重新创建群集。

> [!NOTE]  
> 只支持使用 HDInsight 3.1.3 或更高版本的群集。 如果不确定群集的版本，可以查看“属性”页面。  请参阅[列出和显示群集](hdinsight-administer-use-portal-linux.md#showClusters)。

更改 HDInsight 支持的每种类型的群集所用数据节点数的影响：

* Apache Hadoop

    可以顺利地增加正在运行的 Hadoop 群集中的辅助节点数，而不会影响任何挂起或运行中的作业。 也可在操作进行中提交新作业。 系统会正常处理失败的缩放操作，让群集始终保持正常运行状态。

    减少数据节点数目以缩减 Hadoop 群集时，系统会重新启动群集中的某些服务。 重启服务将导致所有正在运行和挂起的作业在缩放操作完成时失败。 但是，可以在操作完成后重新提交这些作业。
* Apache HBase

    可以顺利地在 HBase 群集运行时对其添加或删除节点。 在完成缩放操作后的几分钟内，区域服务器就能自动平衡。 不过，也可手动均衡区域服务器，方法是登录到群集的头节点，然后在命令提示符窗口中运行以下命令：

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm

    可以顺利地在 Storm 群集运行时对其添加或删除数据节点。 但是，缩放操作成功完成后，需要重新平衡拓扑。

    可以使用两种方法来完成重新平衡操作：

  * Storm Web UI
  * 命令行界面 (CLI) 工具

    有关详细信息，请参阅 [Apache Storm 文档](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 群集上提供了 Storm Web UI：

    ![hdinsight storm 缩放重新平衡](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    以下是有关如何使用 CLI 命令重新平衡 Storm 拓扑的示例：

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

若要使用 Azure PowerShell 更改 Hadoop 群集大小，请从客户端计算机运行以下命令：

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>授予/撤消访问权限
HDInsight 群集提供以下 HTTP Web 服务（所有这些服务都有 REST 样式的终结点）：

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

默认情况下，将授权这些服务进行访问。 可以撤消/授予访问权限。 若要撤消：

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

若要授予：

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> 授予/撤销访问权限时，将重设群集用户的用户名和密码。

也可通过门户执行授予和撤消访问权限。 请参阅[管理 Apache Hadoop 群集在 HDInsight 中使用 Azure 门户](hdinsight-administer-use-portal-linux.md)。

## <a name="update-http-user-credentials"></a>更新 HTTP 用户凭据
此过程与授予/撤销 HTTP 访问权限相同。 如果已授予群集 HTTP 访问权限，必须先撤销该权限。  然后再使用新的 HTTP 用户凭据授予访问权限。

## <a name="find-the-default-storage-account"></a>查找默认存储帐户
以下 PowerShell 脚本演示了如何获取群集的默认存储帐户名称和相关信息：

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>查找资源组
在 Resource Manager 模式下，每个 HDInsight 群集都属于一个 Azure 资源组。  若要查找资源组：

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>提交作业
**提交 MapReduce 作业**

请参阅[运行 HDInsight 随附的 MapReduce 示例](hadoop/apache-hadoop-run-samples-linux.md)。

**提交 Apache Hive 作业**

请参阅[使用 PowerShell 运行 Apache Hive 查询](hadoop/apache-hadoop-use-hive-powershell.md)。

**提交 Apache Pig 作业**

请参阅[使用 PowerShell 运行 Apache Pig 作业](hadoop/apache-hadoop-use-pig-powershell.md)。

**提交 Apache Sqoop 作业**

请参阅[将 Apache Sqoop 与 HDInsight 配合使用](hadoop/hdinsight-use-sqoop.md)。

**提交 Apache Oozie 作业**

请参阅[在 HDInsight 中将 Apache Oozie 与 Apache Hadoop 配合使用以定义和运行工作流](hdinsight-use-oozie.md)。

## <a name="upload-data-to-azure-blob-storage"></a>将数据上传到 Azure Blob 存储
请参阅[将数据上传到 HDInsight][hdinsight-upload-data]。

## <a name="see-also"></a>另请参阅
* [HDInsight cmdlet 参考文档](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-portal-linux.md)
* [使用命令行接口管理 HDInsight][hdinsight-admin-cli]
* [创建 HDInsight 群集][hdinsight-provision]
* [将数据上传到 HDInsight][hdinsight-upload-data]
* [以编程方式提交 Apache Hadoop 作业][hdinsight-submit-jobs]
* [Azure HDInsight 入门][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
