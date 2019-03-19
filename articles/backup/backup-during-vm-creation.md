---
title: 在创建过程中启用 Azure VM 备份
description: 如何在创建过程中启用 Azure 虚拟机备份。
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780437"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>在创建 Azure 虚拟机启用备份

使用 Azure 备份服务来备份 Azure 虚拟机 (Vm)。 根据备份策略中指定的计划备份的 Vm 和从备份创建恢复点。 恢复点存储在恢复服务保管库中。

在 Azure 门户中，这篇文章详细介绍如何启用备份，而你要创建虚拟机 (VM)。  

## <a name="sign-in-to-azure"></a>登录 Azure

如果你不已登录到你的帐户，登录到[Azure 门户](https://portal.azure.com)。
 
## <a name="create-a-vm-with-backup-configured"></a>使用配置的备份创建的 VM 

1. 在 Azure 门户的左上角，选择**新建**。

1. 选择**计算**，然后选择 VM 的映像。

1. 输入 VM 的信息。 用户名和密码，你提供将用于登录到 VM。 完成后，选择**确定**。 

1. 为 VM 选择大小。  

1. 下**设置** > **备份**，选择**已启用**打开备份配置设置。

   - 若要接受默认值，请选择**确定**上**设置**页。 然后将转到**摘要**页后，可以创建 VM。 跳过步骤 6 至 8。
   - 若要更改备份配置值，请执行后续步骤。  

1. 创建或选择一个恢复服务保管库，以保存 VM 的备份。 如果要创建恢复服务保管库，可以选择在保管库的资源组。  

    ![中的 VM 创建页的备份配置设置](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > 恢复服务保管库的资源组可以不同于 VM 的资源组。  

1. 默认情况下，选择备份策略时为你要保护的 VM。 备份策略指定频繁执行备份快照的方式和保留这些备份副本的时间长度。 

   - 可以接受默认策略，也可以创建或选择一个不同的备份策略。 
   - 若要编辑备份策略，请选择**备份策略**和更改的值。  

1. 完成设置备份配置值后，选择**确定**上**设置**页。  

1. 上**摘要**页上之后通过验证后，选择**创建**若要创建的 VM，使用配置的备份设置。 

## <a name="start-a-backup-after-creating-the-vm"></a>创建 VM 后启动备份 

即使已为 VM 配置备份策略，最好创建初始备份。 

VM 创建模板完成后，请转到**Operations**在左侧的菜单中，选择**备份**若要查看虚拟机的备份详细信息。 可以使用此页：

- 触发按需备份。
- 还原完整 VM 或所有磁盘。
- 从 VM 备份还原文件。
- 更改与 VM 关联的备份策略。  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用资源管理器模板部署一个受保护的虚拟机

前面的步骤说明如何使用 Azure 门户创建虚拟机并在恢复服务保管库中对其进行保护。 若要快速部署一个或多个虚拟机和恢复服务保管库保护它们，请参阅模板[部署 Windows VM 并启用备份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。

## <a name="frequently-asked-questions"></a>常见问题 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>哪些映像支持在 VM 创建期间的备份配置？

支持以下核心映像 Microsoft 发布的 VM 创建过程中启用备份。 对于其他 Vm，可以在创建 VM 后启用备份。 若要了解详细信息，请参阅[创建 VM 后启用备份](quick-backup-vm-portal.md)。

- **Windows** -Windows Server 2016 Datacenter、 Windows Server 2016 Datacenter Core Windows Server 2012 Datacenter，Windows Server 2012 R2 Datacenter、 Windows Server 2008 R2 SP1 
- **Ubuntu** -Ubuntu Server 17.10、 Ubuntu 服务器 17.04 和 Ubuntu Server 16.04 (LTS)，Ubuntu Server 14.04 (LTS) 
- **Red Hat** - RHEL 6.7、6.8、6.9、7.2、7.3、7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4、12 SP2、12 SP3 
- **Debian** - Debian 8、Debian 9 
- **CentOS** - CentOS 6.9、CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7、6.8、6.9、7.2、7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>备份成本包含在 VM 成本是多少？ 

不是。 备份成本是独立于 VM 的成本。 有关备份定价详细信息，请参阅[Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>在 VM 上启用备份需要哪些权限？ 

如果你是 VM 参与者，您可以启用 VM 上的备份。 如果您使用的自定义角色，需要以下权限才能启用 VM 上的备份： 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
如果您的恢复服务保管库和 VM 具有不同的资源组，请确保在恢复服务保管库的资源组中具有写入权限。  

## <a name="next-steps"></a>后续步骤 

现在，已保护了 VM，请参阅以下文章，了解如何管理和还原的 Vm:

- [管理和监视虚拟机](backup-azure-manage-vms.md) 
- [恢复虚拟机](backup-azure-arm-restore-vms.md) 
