---
title: "复制 HYPER-V 虚拟机使用 PowerShell 和 Azure 资源管理器 |Microsoft 文档"
description: "自动执行到与 Azure Site Recovery 使用 PowerShell 和 Azure 资源管理器的 Azure 的 HYPER-V 虚拟机的复制。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: dbd562b73b0caecd0feb993bd6fb796dddb0438c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>使用 PowerShell 和 Azure 资源管理器在本地 HYPER-V 虚拟机和 Azure 之间复制
> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell 的资源管理器](site-recovery-deploy-with-powershell-resource-manager.md)
> * [经典门户](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>概述
Azure Site Recovery 有助于业务连续性和灾难恢复策略安排复制、 故障转移和大量部署方案中的虚拟机的恢复。 有关部署方案的完整列表，请参阅[Azure Site Recovery 概述](site-recovery-overview.md)。

Azure PowerShell 是一个模块，提供用于通过 Windows PowerShell 管理 Azure 的 cmdlet。 它就能使用两种类型的模块： Azure 配置文件模块中或 Azure 资源管理器模块。

站点恢复 PowerShell cmdlet，使用 Azure PowerShell 的 Azure 资源管理器中，可帮助你保护和恢复你的服务器在 Azure 中。

本文介绍如何使用 Windows PowerShell，以及 Azure 资源管理器中，部署 Site Recovery，以配置和安排到 Azure 的服务器保护。 在本文中使用的示例演示了如何保护、 故障转移，并通过使用 Azure PowerShell 与 Azure 资源管理器中恢复到 Azure，HYPER-V 主机上的虚拟机。

> [!NOTE]
> 当前，Site Recovery PowerShell cmdlet 允许你配置以下各项： 虚拟机管理器站点、 虚拟机管理器站点到 Azure，并且 HYPER-V 站点到 Azure。
>
>

你不需要专家 PowerShell 以使用本文中，但你需要了解基本概念，如模块、 cmdlet 和会话。 有关 Windows PowerShell 的详细信息，请参阅[Getting Started with Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx)。

可以还阅读更多有关[使用 Azure PowerShell 与 Azure 资源管理器](../powershell-azure-resource-manager.md)。

> [!NOTE]
> 是云解决方案提供程序 (CSP) 程序的一部分的 Microsoft 合作伙伴可以配置和管理的客户的服务器到其客户各自的 CSP 订阅 （租户订阅） 的保护。
>
>

## <a name="before-you-start"></a>开始之前
请确保你准备好这些先决条件：

* A [Microsoft Azure](https://azure.microsoft.com/)帐户。 你可以使用启动[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 此外，你可以阅读[Azure Site Recovery Manager 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。
* Azure PowerShell 1.0。 有关此版本和如何安装它的信息，请参阅[Azure PowerShell 1.0。](https://azure.microsoft.com/)
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/)和[AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/)模块。 你可以从这些模块的最新版本[PowerShell 库](https://www.powershellgallery.com/)

本文将演示如何使用 Azure Powershell 与 Azure 资源管理器来配置和管理你的服务器的保护。 在本文中使用的示例演示了如何保护运行的虚拟机，在 HYPER-V 主机上，到 Azure。 以下先决条件是特定于此示例。 有关更完整的各种 Site Recovery 方案的要求集，请参阅与该方案相关的文档。

* 运行 Windows Server 2012 R2 或包含一个或多个虚拟机的 Microsoft HYPER-V Server 2012 R2 的 HYPER-V 主机。
* HYPER-V 服务器连接到 Internet，直接或通过代理。
* 要保护的虚拟机应符合[虚拟机先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

## <a name="step-1-sign-in-to-your-azure-account"></a>步骤 1： 登录到你的 Azure 帐户
1. 打开 PowerShell 控制台并运行以下命令以登录到你的 Azure 帐户。 该 cmdlet 将打开一个网页，将提示你输入你的帐户凭据。

        Login-AzureRmAccount

    或者，你可能还包含你的帐户凭据作为参数传递给`Login-AzureRmAccount`cmdlet，通过使用`-Credential`参数。

    如果你是代表租户的 CSP 合作伙伴，则使用其 tenantID 或租户的主域名称将客户指定为租户。

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. 一个帐户可以有多个订阅，因此必须将想要使用的帐户的订阅相关联。

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. 验证你的订阅已注册用于 Azure 的提供程序进行恢复服务和 Site Recovery，通过使用以下命令：

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   这些命令的输出中如果**RegistrationState**设置为**已注册**，你可以继续执行步骤 2。 如果没有，您应在你的订阅中注册缺少提供程序。

   若要以进行站点恢复和恢复服务注册 Azure 提供程序，请运行以下命令：

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   验证提供程序注册已成功通过使用以下命令：`Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`和`Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`。

## <a name="step-2-set-up-the-recovery-services-vault"></a>步骤 2： 设置恢复服务保管库
1. 创建 Azure 资源管理器资源组将在其中创建保管库，或使用现有资源组。 可以通过使用以下命令来创建新的资源组：

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    其中 $ResourceGroupName 变量包含你想要创建，资源组的名称，$Geo 变量包含要在其中创建资源组 （例如，"巴西南部"） 的 Azure 区域。

    你也可以通过使用你的订阅中获取资源组的列表`Get-AzureRmResourceGroup`cmdlet。
2. 创建新的 Azure 恢复服务保管库，如下所示：

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    你可以使用检索的现有保管库列表`Get-AzureRmRecoveryServicesVault`cmdlet。

> [!NOTE]
> 如果你想要在创建使用经典门户或 Azure 服务管理 PowerShell 模块的 Site Recovery 保管库上执行操作，你可以通过使用来检索此类保管库的列表`Get-AzureRmSiteRecoveryVault`cmdlet。 你应创建新的恢复服务保管库对所有新的操作。 你已在前面创建的站点恢复保管库支持，但没有最新功能。
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>步骤 3： 设置的恢复服务保管库上下文
1. 通过运行以下命令设置保管库上下文：

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>步骤 4： 创建 HYPER-V 站点，并生成新的保管库注册密钥的站点。
1. 创建新的 HYPER-V 站点，如下所示：

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    此 cmdlet 启动站点恢复作业创建站点，并返回一个 Site Recovery 作业对象。 等待作业完成，验证作业已成功完成。

    你可以检索作业对象，并使用 Get AzureRmSiteRecoveryJob cmdlet，从而检查作业的当前状态。
2. 生成并下载注册密钥的站点，如下所示：

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    将下载的密钥复制到 HYPER-V 主机。 需要密钥才能注册到站点的 HYPER-V 主机。

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>步骤 5： 在 HYPER-V 主机上安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理
1. 下载提供程序从最新版本的安装程序[Microsoft](https://aka.ms/downloaddra)。
2. 运行安装程序在 HYPER-V 主机上，并在安装结束继续注册步骤。
3. 出现提示时，提供下载的站点注册密钥，并完成注册到站点的 HYPER-V 主机。
4. 验证 HYPER-V 主机注册到站点通过使用以下命令：

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>步骤 6： 创建复制策略，并将其与保护容器关联
1. 创建复制策略，如下所示：

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    请检查返回的作业，以确保复制策略创建成功。

   > [!IMPORTANT]
   > 指定的存储帐户应与你的恢复服务保管库的相同 Azure 区域中，并且必须具有启用地域复制。
   >
   > * 如果指定的恢复存储帐户的类型 Azure 存储 （经典），受保护计算机的故障转移将机恢复到 Azure IaaS （经典）。
   > * 如果指定的恢复存储帐户的类型 Azure 存储空间 （Azure 资源管理器），受保护计算机的故障转移将机恢复到 Azure IaaS （Azure 资源管理器）。
   >
   >
2. 获取保护容器对应于该站点，如下所示：

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. 如下所示启动与复制策略的保护容器的关联：

     $Policy = get AzureRmSiteRecoveryPolicy FriendlyName $PolicyName $associationJob = 开始 AzureRmSiteRecoveryPolicyAssociationJob-策略 $Policy PrimaryProtectionContainer $protectionContainer

   等待关联作业完成，并确保它已成功完成。

## <a name="step-7-enable-protection-for-virtual-machines"></a>步骤 7： 为虚拟机启用保护
1. 获取对应于你想要保护，如下所示，VM 的保护实体：

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. 开始保护虚拟机，如下所示：

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > 指定的存储帐户应与你的恢复服务保管库的相同 Azure 区域中，并且必须具有启用地域复制。
   >
   > * 如果指定的恢复存储帐户的类型 Azure 存储 （经典），受保护计算机的故障转移将机恢复到 Azure IaaS （经典）。
   > * 如果指定的恢复存储帐户的类型 Azure 存储空间 （Azure 资源管理器），受保护计算机的故障转移将机恢复到 Azure IaaS （Azure 资源管理器）。
   >
   > 如果你保护的 VM 有多个磁盘附加到它，通过使用指定操作系统磁盘*OSDiskName*参数。
   >
   >
3. 等待初始复制之后达到受保护的状态的虚拟机。 这可能需要一段时间，具体取决于要复制的数据量和连接到 Azure 的可用上游带宽等因素。 作业状态和 StateDescription 更新，如下所示，在 VM 达到受保护的状态时。

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. 更新恢复属性，如 VM 角色大小和附加虚拟机的网络接口卡到故障转移时的 Azure 网络。

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



## <a name="step-8-run-a-test-failover"></a>步骤 8： 运行测试故障转移
1. 运行测试故障转移作业，如下所示：

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. 验证测试 VM 创建在 Azure 中。 （测试故障转移作业挂起时，在 Azure 中创建测试虚拟机之后。 作业完成通过清理后恢复的作业创建 artefacts 下, 一步中所示。）
3. 完成的测试的故障转移，如下所示：

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>后续步骤
[阅读更多](https://msdn.microsoft.com/library/azure/mt637930.aspx)有关使用 Azure 资源管理器 PowerShell cmdlet 的 Azure Site Recovery。
