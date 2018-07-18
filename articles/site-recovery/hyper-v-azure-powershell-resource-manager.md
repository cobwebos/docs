---
title: 使用 PowerShell 和 Azure 资源管理器复制 Hyper-V VM | Microsoft Docs
description: 在 PowerShell 和 Azure 资源管理器中使用 Azure Site Recovery 将 Hyper-V VM 自动复制到 Azure。
services: site-recovery
author: bsiva
manager: abhiag
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 13456dc53f85f6f26aab222ab0cb499aabb7d1cc
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916225"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>使用 PowerShell 和 Azure 资源管理器对 Hyper-V VM 设置到 Azure 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 有助于业务连续性和灾难恢复 (BCDR) 策略，因为它可以协调 Azure 虚拟机 (VM)、本地 VM 和物理服务器的复制、故障转移和恢复。

本文介绍如何结合使用 Windows PowerShell 和 Azure 资源管理器将 Hyper-V VM 复制到 Azure。 本文中使用的示例演示如何将在 Hyper-V 主机上运行的单个 VM 复制到 Azure。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 提供用于通过 Windows PowerShell 管理 Azure 的 cmdlet。 适用于 Azure 资源管理器的 Azure PowerShell 随附 Site Recovery PowerShell cmdlet，有助于保护和恢复 Azure 中的服务器。

尽管无需成为一名 PowerShell 专家就可以使用本文章，但你还是需要理解诸如模块、cmdlet 和会话等基本概念。 请参阅 [Windows PowerShell 入门](http://technet.microsoft.com/library/hh857337.aspx)和[将 Azure PowerShell 与 Azure 资源管理器配合使用](../powershell-azure-resource-manager.md)。

> [!NOTE]
> 参与云解决方案提供商 (CSP) 计划的 Microsoft 合作伙伴可以根据各自的 CSP 订阅（租户订阅）对客户服务器的保护措施进行配置和管理。
>
>

## <a name="before-you-start"></a>开始之前
确保已满足以下先决条件：

* 一个 [Microsoft Azure](https://azure.microsoft.com/) 帐户。 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 此外，可以阅读 [Azure Site Recovery Manager 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。
* Azure PowerShell 1.0。 有关此版本及其安装方法的信息，请参阅 [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) 和 [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) 模块。 可以从 [PowerShell 库](https://www.powershellgallery.com/)获取这些模块的最新版本

此外，本文中提及的特定示例要求满足以下先决条件：

* 一台运行 Windows Server 2012 R2 或 Microsoft Hyper-V Server 2012 R2 的 Hyper-V 主机，其中包含一个或多个 VM。 Hyper-V 服务器应直接或通过代理连接到 Internet。
* 要复制的 VM 应符合[这些先决条件](hyper-v-azure-support-matrix.md#replicated-vms)。

## <a name="step-1-sign-in-to-your-azure-account"></a>步骤 1：登录到 Azure 帐户

1. 打开 PowerShell 控制台，并运行以下命令以登录到 Azure 帐户。 该 cmdlet 会打开一个网页，提示输入帐户凭据：Connect-AzureRmAccount。
    - 或者，还可使用 -Credential 参数将帐户凭据作为参数包含在 Connect-AzureRmAccount cmdlet 中。
    - 如果是代表租户的 CSP 合作伙伴，则需使用 tenantID 或租户主域名将客户指定为一名租户。 例如：Connect-AzureRmAccount -Tenant "fabrikam.com"
2. 一个帐户可以有多个订阅，因此请将需要使用的订阅与帐户关联在一起：

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. 使用以下命令验证订阅是否已注册，以便将 Azure 提供程序用于恢复服务和 Site Recovery：

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. 验证命令输出中是否将“RegistrationState”设置为“已注册”，如果是，则可继续执行步骤 2。 否则，需要通过运行以下命令注册订阅中缺失的提供程序：

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. 使用以下命令验证提供程序是否已成功注册：

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`。

## <a name="step-2-set-up-the-vault"></a>步骤 2：设置保管库

1. 创建一个可在其中创建保管库的 Azure 资源管理器资源组，或者使用现有资源组。 创建新资源组，如下所示。 $ResourceGroupName 变量包含需要创建的资源组的名称，$Geo 变量包含可在其中创建资源组的 Azure 区域（例如：“巴西南部”）。

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. 若要获取订阅中的资源组列表，请运行 Get-AzureRmResourceGroup cmdlet。
2. 创建如下所示的新的 Azure 恢复服务保管库：

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    可使用 Get-AzureRmRecoveryServicesVault cmdlet 检索现有保管库的列表。


## <a name="step-3-set-the-recovery-services-vault-context"></a>步骤 3：设置恢复服务保管库上下文

设置保管库上下文，如下所示：

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>步骤 4：创建 Hyper-V 站点

1. 创建新的 Hyper-V 站点，如下所示：

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. 此 cmdlet 会启动一个创建该站点所需的站点恢复作业，并返回一个站点恢复作业对象。 等待作业完成，并验证作业已成功完成。
3. 使用 Get-AzureRmSiteRecoveryJob cmdlet 检索作业对象，并查看作业的当前状态。
4. 生成和下载站点的注册密钥，如下所示：

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. 将已下载的密钥复制到 Hyper-V 主机。 需要通过该密钥将 Hyper-V 主机注册到站点。

## <a name="step-5-install-the-provider-and-agent"></a>步骤 5：安装提供程序和代理

1. 从 [Microsoft](https://aka.ms/downloaddra) 下载最新版提供程序的安装程序。
2. 在 Hyper-V 主机上运行安装程序。
3. 在安装结束时继续执行注册步骤。
4. 在系统提示时提供下载的密钥，然后完成 Hyper-V 主机的注册过程。
5. 验证 Hyper-V 主机是否已注册到站点，如下所示：

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>步骤 6：创建复制策略

在开始前，请注意指定的存储帐户应与保管库处于同一 Azure 区域，并且应已启用异地复制。

1. 创建复制策略，如下所示：

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. 检查返回的作业，确保复制策略创建成功。

3. 检索对应于该站点的保护容器，如下所示：

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. 将保护容器与复制策略相关联，如下所示：

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

4. 等待关联作业成功完成。

## <a name="step-7-enable-vm-protection"></a>步骤 7：启用 VM 保护

1. 检索与要保护的 VM 相对应的保护实体，如下所示：

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. 保护 VM。 如果要保护的 VM 有多个附加磁盘，则需使用 *OSDiskName* 参数指定操作系统磁盘。

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. 等待 VM 在完成初始复制后进入受保护状态。 这可能需要一段时间，具体取决于诸如要复制的数据量和 Azure 的可用上游带宽等因素。 进入受保护状态后，更新作业状态和 StateDescription，如下所示： 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. 更新各种恢复属性（例如 VM 角色大小），以及进行故障转移后需要将 VM NIC 连接到的 Azure 网络。

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>步骤 8：运行测试故障转移
1. 按如下所述运行测试故障转移：

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. 验证是否在 Azure 中创建了测试 VM。 在 Azure 中创建测试 VM 之后，暂停测试故障转移作业。
3. 若要清理并完成测试故障转移，请运行：

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>后续步骤
[详细了解](https://msdn.microsoft.com/library/azure/mt637930.aspx) Azure Site Recovery 和 Azure 资源管理器 PowerShell cmdlet。
