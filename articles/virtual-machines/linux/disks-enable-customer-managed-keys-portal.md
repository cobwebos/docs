---
title: Azure 门户-通过 SSE 托管磁盘启用客户管理的密钥
description: 通过 Azure 门户在托管磁盘上启用客户管理的密钥。
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235822"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>使用服务器端加密托管磁盘启用客户管理的密钥-门户

如果选择此项，Azure 磁盘存储允许你在使用托管磁盘的服务器端加密 (SSE) 时管理自己的密钥。 有关包含客户托管密钥的 SSE 以及其他托管磁盘加密类型的概念信息，请参阅磁盘加密文章的[客户托管密钥](disk-encryption.md#customer-managed-keys)部分。

## <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，则必须[复制所有数据](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)到完全不同的托管磁盘（未使用客户托管密钥）。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则不会将与托管磁盘关联的托管标识传输到新租户，因此客户管理的密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="next-steps"></a>后续步骤

- [探索 Azure 资源管理器模板，用于创建具有客户管理密钥的加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)) 
- [使用启用了客户托管密钥的磁盘来复制计算机](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 设置 VMware VM 到 Azure 的灾难恢复](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure 资源管理器为 Hyper-V VM 设置到 Azure 的灾难恢复](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
