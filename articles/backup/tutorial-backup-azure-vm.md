---
title: "在 Azure 中大规模备份 Azure VM | Microsoft Docs"
description: "本教程详细介绍了如何将多个 Azure 虚拟机备份到恢复服务保管库。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "虚拟机备份; 备份虚拟机; 备份和灾难恢复"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 01609c00c6f0585eff4843932b9eb7a090a59c19
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>在 Azure 中大规模备份 Azure 虚拟机

本教程详细介绍了如何将 Azure 虚拟机备份到恢复服务保管库。 备份虚拟机的大部分工作是准备工作。 在备份（或保护）虚拟机之前，必须完成[先决条件](backup-azure-arm-vms-prepare.md)中的步骤来准备好保护 VM 的环境。 

> [!IMPORTANT]
> 本教程假定已创建资源组和 Azure 虚拟机。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)是保存要备份项的恢复点的容器。 恢复服务保管库是一种 Azure 资源，可以作为 Azure 资源组的一部分进行部署和管理。 在本教程中，在保护虚拟机的相同资源组中创建恢复服务保管库。


首次使用 Azure 备份时，必须使用订阅注册 Azure 恢复服务提供程序。 如果已使用订阅注册了该提供程序，请转到下一步。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

使用 **New-AzureRmRecoveryServicesVault** 创建恢复服务保管库。 请确保指定在配置要备份的虚拟机时使用的资源组名称和位置。 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。 出于此原因，在变量中存储备份恢复服务保管库对象可提供方便。 然后，使用 **Set-AzureRmRecoveryServicesBackupProperties** 将 **-BackupStorageRedundancy** 选项设置为[异地冗余存储 (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)。 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>备份 Azure 虚拟机

必须设置保管库上下文，才能运行初始备份。 保管库上下文是在保管库中受保护的数据的类型。 在创建恢复服务保管库时，它附带了默认的保护和保留策略。 默认保护策略在每天的指定时间触发备份作业。 默认保留策略将每日恢复点保留 30 天。 在本教程中，接受默认策略。 

使用 **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** 设置保管库上下文。 设置保管库上下文后，它将应用于所有后续 cmdlet。 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

使用 **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** 触发备份作业。 备份作业将创建恢复点。 如果它是初始备份，恢复点将是一个完整备份。 后续备份将创建增量副本。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>删除恢复服务保管库

若要删除恢复服务保管库，必须首先删除保管库中的任何恢复点，然后将保管库取消注册。 以下命令详细介绍了这些步骤。 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>排查错误
如果在备份虚拟机时遇到问题，请参阅[备份 Azure 虚拟机故障排除文章](backup-azure-vms-troubleshoot.md)以获取帮助。

## <a name="next-steps"></a>后续步骤
既然你的虚拟机已受到保护，请参阅以下文章，了解管理任务以及如何从恢复点还原虚拟机。

* 若要修改备份策略，请参阅[使用 AzureRM.RecoveryServices.Backup cmdlet 来备份虚拟机](backup-azure-vms-automation.md#create-a-protection-policy)。
* [管理和监视虚拟机](backup-azure-manage-vms.md)
* [恢复虚拟机](backup-azure-arm-restore-vms.md)
