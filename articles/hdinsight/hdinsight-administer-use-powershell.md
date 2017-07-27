---
title: "使用 PowerShell 管理 HDInsight 中的 Hadoop 群集 - Azure | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 执行针对 HDInsight 中 Hadoop 的管理任务。"
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: c47dabd7c4aa4ba0be08c419989e536711f03677
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 HDInsight 中的 Hadoop 群集
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 是一个功能强大的脚本编写环境，可用于在 Azure 中控制和自动执行工作负荷的部署和管理。 在本文中，你将要学习如何通过使用 Windows PowerShell 借助于本地 Azure PowerShell 控制台来管理 Azure HDInsight 中的 Hadoop 群集。 有关 HDInsight PowerShell cmdlet 的列表，请参阅 [HDInsight cmdlet 参考][hdinsight-powershell-reference]。

**先决条件**

在开始阅读本文前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

如果已安装 Azure PowerShell 版本 0.9x，则必须先卸载该版本，然后再安装新版本。

若要检查所安装的 PowerShell 版本，请执行以下操作：

    Get-Module *azure*

若要卸载旧版本，请运行控制面板中的“程序和功能”。

## <a name="create-clusters"></a>创建群集
请参阅[使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>列出群集
使用以下命令可列出当前订阅中的所有群集：

    Get-AzureRmHDInsightCluster

## <a name="show-cluster"></a>显示群集
使用以下命令可显示当前订阅中特定群集的详细信息：

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

## <a name="delete-clusters"></a>删除群集
使用以下命令来删除群集：

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

还可通过删除包含该群集的资源组删除群集。 请注意，这将会删除组中的所有资源（包括默认存储帐户）。

    Remove-AzureRmResourceGroup -Name <Resource Group Name>

## <a name="scale-clusters"></a>缩放群集
群集缩放功能可让你更改 Azure HDInsight 中运行的群集使用的辅助节点数，而无需重新创建群集。

> [!NOTE]
> 只支持使用 HDInsight 3.1.3 或更高版本的群集。 如果你不确定群集的版本，可以查看“属性”页。  请参阅[列出和显示群集](hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。
>
>

更改 HDInsight 支持的每种类型的群集所用数据节点数的影响：

* Hadoop

    你可以顺利地增加正在运行的 Hadoop 群集中的辅助节点数，而不会影响任何挂起或运行中的作业。 你还可以在操作进行中提交新作业。 系统会正常处理失败的缩放操作，让群集始终保持正常运行状态。

    减少数据节点数目以缩减 Hadoop 群集时，系统会重新启动群集中的某些服务。 这会导致所有正在运行和挂起的作业在缩放操作完成时失败。 但是，你可以在操作完成后重新提交这些作业。
* HBase

    你可以顺利地在 HBase 群集运行时对其添加或删除节点。 在完成缩放操作后的几分钟内，区域服务器就能自动平衡。 不过，也可以手动平衡区域服务器，方法是登录到群集的头节点，然后在命令提示符窗口中运行以下命令：

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer
* Storm

    你可以顺利地在 Storm 群集运行时对其添加或删除数据节点。 但是，在缩放操作成功完成后，你需要重新平衡拓扑。

    可以使用两种方法来完成重新平衡操作：

  * Storm Web UI
  * 命令行界面 (CLI) 工具

    有关详细信息，请参阅 [Apache Storm 文档](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 群集上提供了 Storm Web UI：

    ![hdinsight storm 缩放重新平衡](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    以下是有关如何使用 CLI 命令重新平衡 Storm 拓扑的示例：

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

若要使用 Azure PowerShell 更改 Hadoop 群集大小，请从客户端计算机运行以下命令：

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>


## <a name="grantrevoke-access"></a>授予/撤消访问权限
HDInsight 群集提供以下 HTTP Web 服务（所有这些服务都有 REST 样式的终结点）：

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

默认情况下，将授权这些服务进行访问。 你可以撤消/授予访问权限。 若要撤消：

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

若要授予：

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

> [!NOTE]
> 授予/撤消访问权限时，你将重设群集用户的用户名和密码。
>
>

也可以使用门户完成此操作。 请参阅[使用 Azure 门户管理 HDInsight][hdinsight-admin-portal]。

## <a name="update-http-user-credentials"></a>更新 HTTP 用户凭据
这与[授予/撤消 HTTP 访问权限](#grant/revoke-access)是同一过程。如果已授予群集 HTTP 访问权限，则必须先撤消该访问权限。  然后再使用新的 HTTP 用户凭据授予访问权限。

## <a name="find-the-default-storage-account"></a>查找默认存储帐户
以下 Powershell 脚本演示如何获取群集的默认存储帐户名称和默认存储帐户密钥。

    $clusterName = "<HDInsight Cluster Name>"

    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

## <a name="find-the-resource-group"></a>查找资源组
在 Resource Manager 模式下，每个 HDInsight 群集都属于一个 Azure 资源组。  若要查找资源组：

    $clusterName = "<HDInsight Cluster Name>"

    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


## <a name="submit-jobs"></a>提交作业
**提交 MapReduce 作业**

请参阅[在基于 Windows 的 HDInsight 中运行 Hadoop MapReduce 示例](hdinsight-run-samples.md)。

**提交 Hive 作业**

请参阅[使用 PowerShell 运行 Hive 查询](hdinsight-hadoop-use-hive-powershell.md)。

**提交 Pig 作业**

请参阅[使用 PowerShell 运行 Pig 作业](hdinsight-hadoop-use-pig-powershell.md)。

**提交 Sqoop 作业**

请参阅[将 Sqoop 与 HDInsight 配合使用](hdinsight-use-sqoop.md)。

**提交 Oozie 作业**

请参阅[在 HDInsight 中将 Oozie 与 Hadoop 配合使用以定义和运行工作流](hdinsight-use-oozie.md)。

## <a name="upload-data-to-azure-blob-storage"></a>将数据上传到 Azure Blob 存储
请参阅[将数据上传到 HDInsight][hdinsight-upload-data]。

## <a name="see-also"></a>另请参阅
* [HDInsight cmdlet 参考文档][hdinsight-powershell-reference]
* [使用 Azure 门户管理 HDInsight][hdinsight-admin-portal]
* [使用命令行接口管理 HDInsight][hdinsight-admin-cli]
* [创建 HDInsight 群集][hdinsight-provision]
* [将数据上传到 HDInsight][hdinsight-upload-data]
* [以编程方式提交 Hadoop 作业][hdinsight-submit-jobs]
* [Azure HDInsight 入门][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

