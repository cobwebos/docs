---
title: 教程：使用 Azure 自动化 runbook 创建群集 - Azure HDInsight
description: 了解如何使用 Azure 自动化 runbook 创建和删除包含在云中运行的脚本的 Azure HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553117"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>教程：通过 Azure 自动化创建 Azure HDInsight 群集

使用 Azure 自动化可创建在云中运行的脚本，并按需或按计划管理 Azure 资源。 本文介绍如何通过创建 PowerShell runbook 来创建和删除 Azure HDInsight 群集。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 安装与 HDInsight 交互所需的模块。
> * 创建并存储群集创建期间所需的凭据。
> * 创建新的 Azure 自动化 runbook 以创建 HDInsight 群集。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

* 现有的 [Azure 自动化帐户](../automation/automation-quickstart-create-account.md)。
* 现有的 [Azure 存储帐户](../storage/common/storage-account-create.md)，它将用作群集存储。

## <a name="install-hdinsight-modules"></a>安装 HDInsight 模块

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 Azure 自动化帐户。
1. 在“共享资源”下选择“模块库”。  
1. 在框中键入 AzureRM.Profile，并按 Enter 进行搜索  。 选择可用的搜索结果。
1. 在 AzureRM.profile 屏幕上，选择“导入”   。 选中“更新 Azure 模块”对应的框，然后选择“确定”  。

    ![导入 AzureRM.profile 模块](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. 返回到模块库，方法是：在“共享资源”下选择“模块库”。  
1. 键入 HDInsight。  选择 AzureRM.HDInsight  。

    ![浏览 HDInsight 模块](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. 在 AzureRM.HDInsight 面板上，依次选择“导入”和“确定”    。

    ![导入 AzureRM.HDInsight 模块](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>创建凭据

1. 在“共享资源”下，选择“凭据”   。
1. 选择“添加凭据”  。
1. 在“新凭据”面板上输入所需的信息  。 此凭据用于存储群集密码，你将使用它来登录 Ambari。

    | properties | 值 |
    | --- | --- |
    | 名称 | `cluster-password` |
    | 用户名 | `admin` |
    | 密码 | `SECURE_PASSWORD` |
    | 确认密码 | `SECURE_PASSWORD` |

1. 选择“创建”  。
1. 对新凭据 `ssh-password` 重复相同的过程，并使用用户名 `sshuser` 和所选的密码。 选择“创建”  。 此凭据用于存储群集的 SSH 密码。

    ![创建凭据](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>创建 runbook 以创建群集

1. 在“过程自动化”下，选择“Runbook”。  
1. 选择“创建 Runbook”  。
1. 在“创建 runbook”面板上，输入 runbook 的名称，如 `hdinsight-cluster-create`  。 在“Runbook 类型”下拉列表中，选择 Powershell   。
1. 选择“创建”  。

    ![创建 runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. 在“编辑 PowerShell Runbook”屏幕上输入以下代码，然后选择“发布”   ：

    ![发布 runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>创建 runbook 以删除群集

1. 在“过程自动化”下，选择“Runbook”。  
1. 选择“创建 Runbook”  。
1. 在“创建 runbook”面板上，输入 runbook 的名称，如 `hdinsight-cluster-delete`  。 在“Runbook 类型”下拉列表中，选择 Powershell   。
1. 选择“创建”  。
1. 在“编辑 PowerShell Runbook”屏幕上输入以下代码，然后选择“发布”   ：

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>执行 Runbook

### <a name="create-a-cluster"></a>创建群集

1. 查看自动化帐户的 Runbook 列表，方法是：在“流程自动化”下选择 Runbook。  
1. 选择 `hdinsight-cluster-create`，或创建群集创建 runbook 时使用的名称。
1. 选择“开始”，立即执行 runbook  。 你还可以安排 runbook 定期运行。 请参阅[在 Azure 自动化中安排运行 Runbook](../automation/shared-resources/schedules.md)
1. 输入脚本所需的参数，然后选择“确定”  。 这会创建一个新的 HDInsight 群集，该群集具有你在 CLUSTERNAME 参数中指定的名称  。

    ![执行创建群集 runbook](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>删除群集

通过选择创建的 `hdinsight-cluster-delete` runbook 删除群集。 选择“开始”，输入 CLUSTERNAME 参数，然后选择“确定”    。

## <a name="clean-up-resources"></a>清理资源

如果不再需要，请删除创建的 Azure 自动化帐户以避免意外的费用。 要执行此操作，请导航到 Azure 门户，选择创建了 Azure 自动化帐户的资源组，选择自动化帐户，然后选择“删除”  "。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-powershell.md)