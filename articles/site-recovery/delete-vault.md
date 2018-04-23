---
title: 删除 Site Recovery 保管库
description: 了解如何根据 Site Recovery 方案删除 Azure Site Recovery 保管库。
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 62792747b4efe2de4c22af6f0886503d7d63ed44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="delete-a-site-recovery-vault"></a>删除 Site Recovery 保管库
依赖项可能会阻止删除 Azure Site Recovery 保管库。 需要采取的措施因 Site Recovery 方案而异：VMware 到 Azure、Hyper-V（含和不含 System Center Virtual Machine Manager）到 Azure 和 Azure 备份。 若要删除用于 Azure 备份的保管库，请参阅[删除 Azure 备份保管库](../backup/backup-azure-delete-vault.md)。



## <a name="delete-a-site-recovery-vault"></a>删除 Site Recovery 保管库 
若要删除保管库，请按照方案对应的推荐步骤操作。

### <a name="vmware-vms-to-azure"></a>VMware VM 到 Azure

1. 按照[禁用 VMware 保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)中的步骤操作，删除所有受保护的 VM。

2. 按照[删除复制策略](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)中的步骤操作，删除所有复制策略。

3. 按照[删除 vCenter 服务器](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)中的步骤操作，删除对 vCenter 的引用。

4. 按照[解除配置服务器授权](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)中的步骤操作，删除配置服务器。

5. 删除保管库。


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Hyper-V VM（带 Virtual Machine Manager）到 Azure
1. 按照[禁用对使用 System Center VMM 到 Azure 方案复制到 Azure 的 Hyper-V 虚拟机的保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)中的步骤操作，删除所有受保护的 VM。

2. 依次转到保管库 ->“Site Recovery 基础结构”->“对于 System Center VMM” -> “复制策略”，取消关联并删除所有复制策略

3.  按照[取消注册已连接的 VMM 服务器](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)中的步骤操作，删除对 Virtual Machine Manager 服务器的引用。

4.  删除保管库。

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM（不带 Virtual Machine Manager）到 Azure
1. 按照[禁用对 Hyper-V 虚拟机（Hyper-V 到 Azure）的保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)中的步骤操作，删除所有受保护的 VM。

2. 依次转到保管库 ->“Site Recovery 基础结构”->“对于 Hyper-V 站点” -> “复制策略”，取消关联并删除所有复制策略

3. 按照[取消注册 Hyper-V 主机](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)中的步骤操作，删除对 Hyper-V 服务器的引用。

4. 删除 Hyper-V 站点。

5. 删除保管库。


## <a name="use-powershell-to-force-delete-the-vault"></a>使用 PowerShell 强制删除保管库 

> [!Important]
> 若要测试产品，并对数据丢失并不在意，请使用强制删除方法，快速删除保管库及其所有依赖项。
> PowerShell 命令将删除保管库的所有内容，这一步无法撤消。

若要删除 Site Recovery 保管库（即使有受保护的项，也不例外），请运行以下命令：

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault
