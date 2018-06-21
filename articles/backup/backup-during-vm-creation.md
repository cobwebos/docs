---
title: 在创建过程中启用 Azure VM 备份
description: 查看在创建过程中启用 Azure 虚拟机备份的步骤。
services: backup, virtual-machines
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 928481f07875286a21f68dae6556f04eb2b6ae5c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606114"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>在 Azure 虚拟机创建过程中启用备份 

Azure 备份服务提供创建备份并将其配置到云的接口。 通过定期执行备份（称为恢复点）保护数据。 Azure 备份可创建恢复点，这些恢复点可存储在异地冗余的恢复保管库中。 本文详细介绍了如何在通过 Azure 门户创建虚拟机 (VM) 时启用备份。  

## <a name="log-in-to-azure"></a>登录 Azure 

如果尚未登录帐户，请先登录到 [Azure 门户](http://portal.azure.com)。
 
## <a name="create-virtual-machine-with-backup-configured"></a>使用配置的备份创建虚拟机 

1. 单击 Azure 门户左上角的“新建”。 

2. 选择“计算”，然后选择虚拟机的映像。   

3. 输入虚拟机信息。 提供的用户名和密码用于登录到虚拟机。 完成后，单击“确定”。 

4. 为 VM 选择大小。  

5. 在“设置”>“备份”下，单击“已启用”打开备份配置设置。 可以接受默认值并单击设置页上的“确定”，转到“摘要”页创建 VM。 如果需要更改值，请按照下面的步骤操作。  

6. 创建或选择恢复服务保管库，其中保存了虚拟机的备份。 如果要创建恢复服务保管库，可以为该保管库选择一个资源组。  

    ![vm 创建页中的备份配置](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > 恢复服务保管库的资源组可能与虚拟机的资源组不同。  
    > 
    > 

7. 默认情况下，已选择备份策略以快速保护 VM。 备份策略指定了执行备份快照的频率和这些备份副本的保留期。 可以接受默认策略，也可以创建或选择一个不同的备份策略。 要编辑备份策略，请选择“备份策略”并更改策略值。  

8. 如果对备份配置值很满意，则在“设置”页中单击“确定”。  

9. 通过验证后，在“摘要”页上单击“创建”，创建使用配置的备份设置的虚拟机。 

## <a name="initiate-a-backup-after-creating-the-vm"></a>在创建 VM 后启动备份 

虽然已创建备份策略，但是最好创建一个初始备份。 要在 VM 创建模板完成后查看虚拟机的备份详细信息，请在左侧菜单上的“操作”设置中单击“备份”。 可通过此操作触发按需备份、还原完整 VM 或所有磁盘、从 VM 备份还原文件，或更改与虚拟机关联的备份策略。  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用资源管理器模板部署一个受保护的 VM

前面的步骤说明了如何使用 Azure 门户来创建虚拟机，以及使用恢复服务保管库来保护该虚拟机。 如果希望快速部署一台或多台虚拟机，并使用恢复服务保管库来保护它们，请参阅模板[部署 Windows VM 并启用备份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。

## <a name="frequently-asked-questions"></a>常见问题 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>哪些映像在创建 VM 时启用备份？ 

下列 Microsoft 发布的核心映像支持在 VM 创建过程中启用备份。 对于其他 VM，可以在创建 VM 后启用备份。 了解有关[在创建 VM 后启用备份](quick-backup-vm-portal.md)的详细信息 

- **Windows** - Windows Server 2016 Data center、Windows Server 2016 Data Center core、Windows Server 2012 DataCenter、Windows Server 2012 R2 DataCenter、Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 1710、Ubuntu Server 1704、UUbuntu Server 1604(LTS)、Ubuntu Server 1404(LTS) 
- **Redhat** - RHEL 6.7、6.8、6.9、7.2、7.3、7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4、12 SP2、12 SP3 
- **Debian** - Debian 8、Debian 9 
- **CentOS** - CentOS 6.9、CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7、6.8、6.9、7.2、7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>备份成本包含在 VM 成本内吗？ 

不是，备份成本是独立于或不同于虚拟机成本的。 有关备份定价的详细信息，请参阅[备份定价站点](https://azure.microsoft.com/pricing/details/backup/)。
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>在 VM 上启用备份需要哪些权限？ 

如果是虚拟机参与者，便可以在 VM 上启用备份。 如果使用的是自定义角色，则需要具有以下权限才可在 VM 上成功启用备份。 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
如果恢复服务保管库和虚拟机的资源组不同，请确保具有恢复服务保管库资源组的写入权限。  

## <a name="next-steps"></a>后续步骤 

现已保护 VM，请参阅以下文章了解 VM 管理任务以及如何还原 VM。 

- [管理和监视虚拟机](backup-azure-manage-vms.md) 
- [恢复虚拟机](backup-azure-arm-restore-vms.md) 
