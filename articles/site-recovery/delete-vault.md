---
title: "删除恢复服务保管库"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>删除恢复服务保管库
依赖项导致无法删除恢复服务保管库，需要采取的措施因 Azure Site Recovery 方案的类型（从 VMWare 恢复到 Azure、从 Hyper-V（使用和不使用 VMM）恢复到 Azure 以及 Azure 备份）而异。 若要删除用于 Azure 备份的保管库，请单击[此](../backup/backup-azure-delete-vault.md)链接。

>[!Important]
>若要测试产品，并希望快速删除保管库，对数据丢失并不在意，可以使用强制删除方法，删除保管库及其所有依赖项。

> 请注意，PowerShell 命令将删除保管库中的所有内容，并且无法撤消这一步

## <a name="force-delete-vault-using-powershell"></a>使用 Powershell 强制删除保管库

请按照以下步骤操作，删除 Site Recovery 保管库（即使有受保护的项，也不例外）

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



请按照适用于方案的建议步骤操作（按给定顺序），删除保管库

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>删除在 Site Recovery 中用于保护恢复到 Azure 的 VMWare VM 的保管库：
1. 务必删除所有受保护的 VM。请参阅[具体步骤](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)。
2.  务必删除所有复制策略。请参阅[具体步骤](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。
3.  务必删除对 vCenter 的引用。请参阅[具体步骤](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery)。
4. 务必删除配置服务器。请参阅[具体步骤](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server)。
5. 现在，尝试删除保管库。


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>删除在 Site Recovery 中用于保护恢复到 Azure 的 Hyper-V VM（使用 VMM）的保管库：
1.  务必删除所有受保护的 VM。请参阅[具体步骤](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)。
- 务必删除所有复制策略。请参阅[具体步骤](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。
-   删除对 VMM 服务器的引用。请参阅[具体步骤](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)
-   现在，尝试删除保管库。

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>删除在 Site Recovery 中用于保护恢复到 Azure 的 Hyper-V VM（不使用 VMM）的保管库：
1. 务必删除所有受保护的 VM。请参阅[具体步骤](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)。
- 务必删除所有复制策略。请参阅[具体步骤](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。
-   删除对 Hyper-V 服务器的引用。请参阅[具体步骤](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)。
-   删除 Hyper-V 站点。
-   现在，尝试删除保管库。

