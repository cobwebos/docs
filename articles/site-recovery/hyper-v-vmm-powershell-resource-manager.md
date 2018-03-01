---
title: "使用 PowerShell 将 VMM 云中的 Hyper-V VM 复制到辅助站点（Azure 资源管理器）| Microsoft Docs"
description: "介绍如何使用 PowerShell（资源管理器）将 VMM 云中的 Hyper-V VM 复制到辅助 VMM 站点"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: a5e36546494223b20844303f3f76782746658411
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-using-powershell-resource-manager"></a>使用 PowerShell 将 Hyper-V VM 复制到辅助站点（资源管理器）

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 来自动完成相关步骤，以便将 System Center Virtual Machine Manager (VMM) 云中的 Hyper-V VM 复制到辅助本地站点中的 VMM 云。

## <a name="prerequisites"></a>先决条件

- 查看[方案体系结构和组件](hyper-v-vmm-architecture.md)。
- 查看所有组件的[支持要求](site-recovery-support-matrix-to-sec-site.md)。
- 确保 VMM 服务器和 Hyper-V 主机符合[支持要求](site-recovery-support-matrix-to-sec-site.md)。
- 确保要复制的 VM 符合[复制计算机支持](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)


## <a name="prepare-for-network-mapping"></a>准备网络映射

[网络映射](hyper-v-vmm-network-mapping.md)在源和目标云中的本地 VMM VM 网络之间映射。 映射可执行以下操作：

- 故障转移后，将 VM 连接到适当的目标 VM 网络。 
- 最好将副本 VM 放于目标 Hyper-V 主机服务器上。 
- 如果不配置网络映射，则故障转移后，VM 副本不会连接到 VM 网络。

准备 VMM，如下所示：

1. 确保源服务器和目标 VMM 服务器上具有 [VMM 逻辑网络](https://docs.microsoft.com/system-center/vmm/network-logical)。
    - 源服务器上的逻辑网络应与 Hyper-V 主机所在的源云相关联。
    - 目标服务器上的逻辑网络应与目标云相关联。
1. 确保源服务器和目标 VMM 服务器上具有 [VM 网络](https://docs.microsoft.com/system-center/vmm/network-virtual)。 VM 网络应链接到每个位置中的逻辑网络。
2. 将源 Hyper-V 主机上的 VM 连接到源 VM 网络。 

## <a name="prepare-for-powershell"></a>为 PowerShell 做准备

确保已将 Azure PowerShell 准备就绪。

- 如果已使用 PowerShell，则升级到 0.8.10 或更高版本。  [详细了解](/powershell/azureps-cmdlets-docs)如何设置 PowerShell。
- 设置并配置 PowerShell 后，请参阅[服务 cmdlet](/powershell/azure/overview)。
- 若要详细了解如何在 Azure PowerShell 中使用参数值、输入和输出，请参阅[入门](/powershell/azure/get-started-azureps)指南。

## <a name="set-up-a-subscription"></a>设置订阅
1. 从 Azure PowerShell 登录到 Azure 帐户：

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. 使用订阅 ID 检索订阅列表。 记下希望在其中创建恢复服务保管库的订阅的 ID。   

        Get-AzureRmSubscription
3. 设置保管库的订阅：

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
1. 如果没有 Azure 资源管理器资源组，则创建一个。

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 创建新的恢复服务保管库，并将保管库对象保存在后面会用到的变量中： 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    可以在创建保管库对象后使用 Get-AzureRMRecoveryServicesVault cmdlet 来检索它。

## <a name="set-the-vault-context"></a>设置保管库上下文
1. 检索现有保管库：

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. 设置保管库上下文：

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-azure-site-recovery-provider"></a>安装 Azure Site Recovery 提供程序
1. 在 VMM 计算机上，通过运行以下命令创建一个目录：

       New-Item c:\ASR -type directory
2. 使用下载的提供程序安装文件提取这些文件：pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:。 /q
3. 安装该提供程序，等待安装完成：

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. 在保管库中注册服务器：

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>创建和关联复制策略
1. 创建复制策略（在此示例中，复制策略是针对 Hyper-V 2012 R2 的），如下所示：

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > VMM 云包含的 Hyper-V 主机可能运行不同版本的 Windows Server，但复制策略是针对特定版本的操作系统的。 如果不同的主机运行在不同的操作系统上，则请为每个系统创建不同的复制策略。 例如，如果有 5 个主机运行在 Windows Server 2012 上，3 个主机运行在 Windows Server 2012 R2 上，请创建两个复制策略 - 一个策略用于一种类型的操作系统。

2. 检索主保护容器（主 VMM 云 c）和恢复保护容器（恢复 VMM 云）：

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. 使用友好名称检索所创建的复制策略：

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. 开始将保护容器（VMM 云）与复制策略相关联：

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. 等待策略关联作业完成。 可以使用以下 PowerShell 代码段来查看作业是否已完成：

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   在作业完成处理后，运行以下命令：

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

若要检查作业是否完成，请遵循[监视活动](#monitor-activity)中的步骤。

##  <a name="configure-network-mapping"></a>配置网络映射
1. 使用此命令检索当前保管库的服务器。 此命令将 Azure Site Recovery 服务器存储在 $Servers 数组变量中。

        $Servers = Get-AzureRmSiteRecoveryServer
2. 运行此命令，检索源 VMM 服务器和目标 VMM 服务器的网络。

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > 在服务器数组中，源 VMM 服务器可以是一个，也可以是第二个。 检查 VMM 服务器名称，并相应地检索网络。


3. 此 cmdlet 在主网络与恢复网络之间创建映射。 它将主要网络指定为 $PrimaryNetworks 的第一个元素，将恢复网络指定为 $RecoveryNetworks 的第一个元素。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>为 VM 启用保护
在正确配置服务器、云和网络后，可以在云中为 VM 启用保护。

1. 若要启用保护，请运行以下命令以检索保护容器：

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. 获取保护实体 (VM)，如下所示：

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. 为 VM 启用复制：

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>运行测试故障转移

若要测试部署，可针对一个虚拟机运行测试性故障转移，或者创建一个包括多个 VM 的恢复计划并针对该计划运行测试性故障转移。 测试故障转移在隔离的网络中模拟故障转移和恢复机制。

1. 检索需要将 VM 故障转移到其中的 VM：

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. 按如下所述执行测试性故障转移：
    - 对于单个 VM

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
    - 对于恢复计划：

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

若要检查作业是否完成，请遵循[监视活动](#monitor-activity)中的步骤。

## <a name="run-planned-and-unplanned-failovers"></a>运行计划内和计划外故障转移

1. 执行计划内故障转移，如下所示：
    -  对于单个 VM：

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
    - 对于恢复计划

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan
2. 执行计划外故障转移，如下所示：
    - 对于单个 VM
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

    - 对于恢复计划：

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>监视活动
使用以下命令来监视故障转移活动。 请注意，必须在执行不同的作业之前等待处理完成。

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>后续步骤

[详细了解](/powershell/module/azurerm.recoveryservices.backup/#recovery)如何将 Site Recovery 和 Azure 资源管理器 PowerShell cmdlet 配合使用。
