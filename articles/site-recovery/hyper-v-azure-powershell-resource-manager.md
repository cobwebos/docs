---
title: 使用 Azure 站点恢复和 PowerShell 进行超 V VM 灾难恢复
description: 在 PowerShell 和 Azure 资源管理器中使用 Azure Site Recovery 服务将 Hyper-V VM 自动灾难恢复到 Azure。
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257987"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>使用 PowerShell 和 Azure 资源管理器对 Hyper-V VM 设置到 Azure 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 有助于业务连续性和灾难恢复 (BCDR) 策略，因为它可以协调 Azure 虚拟机 (VM)、本地 VM 和物理服务器的复制、故障转移和恢复。

本文介绍如何结合使用 Windows PowerShell 和 Azure 资源管理器将 Hyper-V VM 复制到 Azure。 本文中使用的示例演示如何将在 Hyper-V 主机上运行的单个 VM 复制到 Azure。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 提供用于通过 Windows PowerShell 管理 Azure 的 cmdlet。 适用于 Azure 资源管理器的 Azure PowerShell 随附 Site Recovery PowerShell cmdlet，有助于保护和恢复 Azure 中的服务器。

尽管无需成为一名 PowerShell 专家就可以使用本文章，但你还是需要理解诸如模块、cmdlet 和会话等基本概念。 有关详细信息，请参阅[PowerShell 文档](/powershell)，并将[Azure PowerShell 与 Azure 资源管理器一起使用](../powershell-azure-resource-manager.md)。

> [!NOTE]
> 参与云解决方案提供商 (CSP) 计划的 Microsoft 合作伙伴可以根据各自的 CSP 订阅（租户订阅）对客户服务器的保护措施进行配置和管理。

## <a name="before-you-start"></a>开始之前

确保已满足以下先决条件：

- 一个 [Microsoft Azure](https://azure.microsoft.com/) 帐户。 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 此外，可以阅读 [Azure Site Recovery Manager 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。
- Azure PowerShell。 若要深入了解此版本及其安装方法，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

此外，本文中提及的特定示例要求满足以下先决条件：

- 一台运行 Windows Server 2012 R2 或 Microsoft Hyper-V Server 2012 R2 的 Hyper-V 主机，其中包含一个或多个 VM。 Hyper-V 服务器应直接或通过代理连接到 Internet。
- 要复制的 VM 应符合[这些先决条件](hyper-v-azure-support-matrix.md#replicated-vms)。

## <a name="step-1-sign-in-to-your-azure-account"></a>步骤 1：登录到 Azure 帐户

1. 打开 PowerShell 控制台，并运行以下命令以登录到 Azure 帐户。 cmdlet 会打开一个网页，提示您输入帐户凭据： `Connect-AzAccount`。
   - 或者，您可以使用`Connect-AzAccount`**凭据**参数将帐户凭据作为参数包含在 cmdlet 中。
   - 如果您是代表租户工作的 CSP 合作伙伴，请使用租户 ID 或租户主域名将客户指定为租户。 例如： `Connect-AzAccount -Tenant "fabrikam.com"`
1. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起：

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. 使用以下命令验证订阅是否已注册，以便将 Azure 提供程序用于恢复服务和 Site Recovery：

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. 验证命令输出中是否将“RegistrationState”设置为“已注册”，如果是，则可继续执行步骤 2********。 否则，需要通过运行以下命令注册订阅中缺失的提供程序：

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. 使用以下命令验证提供程序是否已成功注册：

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>步骤 2：设置保管库

1. 创建一个可在其中创建保管库的 Azure 资源管理器资源组，或者使用现有资源组。 创建新资源组，如下所示。 变量`$ResourceGroupName`包含要创建的资源组的名称，$Geo变量包含要在其中创建资源组的 Azure 区域（例如，"巴西南部"）。

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. 要获取订阅中的资源组列表，请运行`Get-AzResourceGroup`cmdlet。
1. 创建如下所示的新的 Azure 恢复服务保管库：

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

您可以使用`Get-AzRecoveryServicesVault`cmdlet 检索现有保管库的列表。

## <a name="step-3-set-the-recovery-services-vault-context"></a>步骤 3：设置恢复服务保管库上下文

设置保管库上下文，如下所示：

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>步骤 4：创建 Hyper-V 站点

1. 创建新的 Hyper-V 站点，如下所示：

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. 此 cmdlet 会启动一个创建该站点所需的站点恢复作业，并返回一个站点恢复作业对象。 等待作业完成，并验证作业已成功完成。
1. 使用`Get-AzRecoveryServicesAsrJob`cmdlet 检索作业对象，并检查作业的当前状态。
1. 生成和下载站点的注册密钥，如下所示：

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. 将已下载的密钥复制到 Hyper-V 主机。 需要通过该密钥将 Hyper-V 主机注册到站点。

## <a name="step-5-install-the-provider-and-agent"></a>步骤 5：安装提供程序和代理

1. 从[微软](https://aka.ms/downloaddra)下载最新版本的安装程序。
1. 在 Hyper-V 主机上运行安装程序。
1. 在安装结束时继续执行注册步骤。
1. 在系统提示时提供下载的密钥，然后完成 Hyper-V 主机的注册过程。
1. 验证 Hyper-V 主机是否已注册到站点，如下所示：

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

如果运行的是 Hyper-V 核心服务器，请下载安装程序文件并执行以下操作：

1. 通过运行以下命令，从_AzureSite 恢复提供程序.exe_中提取文件到本地目录：

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 运行以下命令：

   ```console
   .\setupdr.exe /i
   ```

   结果记录到 _%程序数据%%\ASRLogs_DRAsetupWizard.log_。

1. 运行此命令注册服务器：

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>步骤 6：创建复制策略

在开始之前，指定的存储帐户应与保管库位于同一 Azure 区域中，并且应启用异地复制。

1. 创建复制策略，如下所示：

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. 检查返回的作业，确保复制策略创建成功。

1. 检索对应于该站点的保护容器，如下所示：

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. 将保护容器与复制策略相关联，如下所示：

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. 等待关联作业成功完成。

1. 检索保护容器映射。

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>步骤 7：启用 VM 保护

1. 检索与要保护的 VM 相对应的可保护项，如下所示：

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. 保护 VM。 如果要保护的 VM 已连接到多个磁盘，请使用**OSDiskName**参数指定操作系统磁盘。

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. 等待 VM 在完成初始复制后进入受保护状态。 这可能需要一段时间，具体取决于诸如要复制的数据量和 Azure 的可用上游带宽等因素。 进入受保护状态后，更新作业状态和 StateDescription，如下所示：

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. 更新恢复属性（如 VM 角色大小）和 Azure 网络，这些网络在故障转移后将 VM NIC 附加到这些属性。

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> 如果要复制到 Azure 中启用的 CMK 托管磁盘，请使用 Az PowerShell 3.3.0 执行以下步骤：
>
> 1. 通过更新 VM 属性，将故障转移启用托管磁盘
> 1. 使用`Get-AzRecoveryServicesAsrReplicationProtectedItem`cmdlet 获取受保护项目的每个磁盘的磁盘 ID
> 1. 使用`New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"`cmdlet 创建字典对象，以包含磁盘 ID 与磁盘加密集的映射。 这些磁盘加密集将由您在目标区域中预先创建。
> 1. 通过在`Set-AzRecoveryServicesAsrReplicationProtectedItem`**DiskIdToDisk 加密集映射**参数中传递字典对象，使用 cmdlet 更新 VM 属性。

## <a name="step-8-run-a-test-failover"></a>步骤 8：运行测试故障转移

1. 按如下所述运行测试故障转移：

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. 验证是否在 Azure 中创建了测试 VM。 在 Azure 中创建测试 VM 之后，暂停测试故障转移作业。
1. 若要清理并完成测试故障转移，请运行：

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>后续步骤

[详细了解](/powershell/module/az.recoveryservices) Azure Site Recovery 和 Azure 资源管理器 PowerShell cmdlet。
