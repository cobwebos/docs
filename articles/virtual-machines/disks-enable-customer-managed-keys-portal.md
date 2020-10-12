---
title: Azure 门户-通过 SSE 托管磁盘启用客户管理的密钥
description: 通过 Azure 门户在托管磁盘上启用客户管理的密钥。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 1b40eef0a4cae8fa68631426ff72003db43d7530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817379"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>使用 Azure 门户对托管磁盘的客户托管密钥启用服务器端加密

Azure 磁盘存储使你能在对托管磁盘使用服务器端加密 (SSE) 时管理自己的密钥（如果你选择）。 有关包含客户托管密钥的 SSE 以及其他托管磁盘加密类型的概念信息，请参阅磁盘加密文章的 **客户托管密钥** 部分：

- 对于 Linux： [客户托管的密钥](./linux/disk-encryption.md#customer-managed-keys)
- 对于 Windows： [客户托管的密钥](./windows/disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此操作，必须将所有数据复制到完全不同的托管磁盘，而不使用客户管理的密钥：

    - 对于 Linux： [复制托管磁盘](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - 对于 Windows： [复制托管磁盘](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- 仅支持2048位、3072位和4096位大小的 [软件和 HSM RSA 密钥](../key-vault/keys/about-keys.md) ，无其他密钥或大小。
    - [HSM](../key-vault/keys/hsm-protected-keys.md) 密钥需要 Azure 密钥保管库的 **高级** 层。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

以下部分介绍如何为托管磁盘启用和使用客户托管的密钥：

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

创建并设置好 Key Vault 和磁盘加密集之后，接下来即可使用加密来部署 VM。
VM 部署过程与标准部署过程类似，唯一的差别在于，你需要将 VM 部署到与其他资源相同的区域中，并选择使用客户托管密钥。

1. 搜索“虚拟机”，然后选择“+ 添加”以创建 VM 。
1. 在 " **基本** " 边栏选项卡中，选择与磁盘加密集相同的区域，并 Azure Key Vault。
1. 根据需要，在 " **基本** " 边栏选项卡中填写其他值。

    ![VM 创建体验的屏幕截图，其中突出显示了区域值。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. 在 " **磁盘** " 边栏选项卡上，选择 " **静态加密"，并使用客户管理的密钥**。
1. 在“磁盘加密集”下拉列表中选择磁盘加密集。
1. 根据需要进行剩余选择。

    ![VM 创建体验的屏幕截图，“磁盘”边栏选项卡。 其中突出显示了“磁盘加密集”下拉列表。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>在现有磁盘上启用

> [!CAUTION]
> 在附加到 VM 的任何磁盘上启用磁盘加密将需要你停止 VM。
    
1. 导航到与磁盘加密集位于同一区域中的 VM。
1. 打开 VM 并选择“停止”。

    ![示例 VM 的主覆盖屏幕截图，其中突出显示了 "停止" 按钮。](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM 停止后，选择“磁盘”，然后选择要加密的磁盘。

    ![示例 VM 的屏幕截图，其中“磁盘”边栏选项卡处于打开状态。 OS 磁盘突出显示，作为示例磁盘供你选择。](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. 依次选择“加密”、“使用客户托管密钥进行静态加密”，然后在下拉列表中选择“磁盘加密集” 。
1. 选择“保存” 。

    ![示例 OS 磁盘的屏幕截图。 “加密”边栏选项卡处于打开状态，“使用客户托管密钥进行静态加密”处于选中状态以及示例 Azure Key Vault。 完成这些选择后，“保存”按钮处于选中状态。](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 对于附加到你想要加密的 VM 的任何其他磁盘，请重复此过程。
1. 当磁盘完成切换到客户托管密钥后，如果没有其他需要进行加密的附加磁盘，则可以启动 VM。

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会转移到新租户，因此，客户托管密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="next-steps"></a>后续步骤

- [探索 Azure 资源管理器模板以使用客户管理密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure 密钥保管库？](../key-vault/general/overview.md)
- [使用启用了客户托管密钥的磁盘来复制计算机](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 设置 VMware VM 到 Azure 的灾难恢复](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure 资源管理器为 Hyper-V VM 设置到 Azure 的灾难恢复](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
