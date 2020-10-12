---
title: 删除 Azure Site Recovery 保管库
description: 了解如何删除为 Azure Site Recovery 配置的恢复服务保管库
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426378"
---
# <a name="delete-a-site-recovery-services-vault"></a>删除 Site Recovery 服务保管库

本文介绍如何删除 Site Recovery 的恢复服务保管库。 若要删除用于 Azure 备份的保管库，请参阅[删除 Azure 备份保管库](../backup/backup-azure-delete-vault.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>开始之前

必须先删除已注册的服务器以及保管库中的项，然后才能删除保管库。 需要删除的内容取决于所部署的复制方案。 


## <a name="delete-a-vault-azure-vm-to-azure"></a>删除保管库-Azure VM 到 Azure

1. 按照[这些说明](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)操作，删除所有受保护的 VM。
2. 然后，删除保管库。

## <a name="delete-a-vault-vmware-vm-to-azure"></a>删除保管库-VMware VM 到 Azure

1. 按照[这些说明](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)操作，删除所有受保护的 VM。
2. 按照[这些步骤](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)操作，删除所有复制策略。
3. 使用[这些步骤](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)删除对 vCenter 的引用。
4. 按照[这些说明](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)操作，停止使用配置服务器。
5. 然后，删除保管库。


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>删除保管库-Hyper-V VM（使用 VMM）到 Azure

1. 按照[这些步骤](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)操作，删除由 System Center VMM 管理的 Hyper-V VM。
2. 取消关联并删除所有复制策略。 在保管库中执行此操作 > “Site Recovery 基础结构” > “对于 System Center VMM” > “复制策略”。
3. 按照[这些步骤](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)操作，取消注册已连接的 VMM 服务器。
4. 然后，删除保管库。

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>删除保管库-Hyper-V VM 到 Azure

1. 按照[这些步骤](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)操作，删除所有受保护的 VM。
2. 取消关联并删除所有复制策略。 在保管库中执行此操作 >“Site Recovery 基础结构” > “对于 Hyper-V 站点” > “复制策略”。
3. 按照[这些说明](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)操作，取消注册 Hyper-V 主机。
4. 删除 Hyper-V 站点。
5. 然后，删除保管库。


## <a name="use-powershell-to-force-delete-the-vault"></a>使用 PowerShell 强制删除保管库 

> [!Important]
> 若要测试产品，并对数据丢失并不在意，请使用强制删除方法，快速删除保管库及其所有依赖项。
> PowerShell 命令将删除保管库的所有内容，这一步无法撤消  。

若要删除 Site Recovery 保管库（即使有受保护的项，也不例外），请运行以下命令：

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

详细了解 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault) 和 [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)。
