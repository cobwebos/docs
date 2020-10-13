---
title: Azure 托管磁盘的服务器端加密 - PowerShell
description: Azure 存储在将数据保存到存储群集之前会对其进行静态加密，以此保护数据。 可以使用 Microsoft 托管密钥加密你的托管磁盘，也可以使用客户托管密钥以通过自己的密钥管理所做的加密。
author: roygara
ms.date: 09/23/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 07916b7f7e5d0f1e755ae9759a16541eede45a41
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950476"
---
# <a name="server-side-encryption-of-azure-disk-storage-for-powershell"></a>针对 PowerShell 的 Azure 磁盘存储的服务器端加密

服务器端加密 (SSE) 可保护数据，并帮助实现组织安全性和符合性承诺。 将存储在 Azure 托管磁盘（OS 和数据磁盘）上的数据保存到云时，SSE 在默认情况下会自动对这些数据进行静态加密。 

Azure 托管磁盘中的数据使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大分组加密之一）以透明方式加密，且符合 FIPS 140-2 规范。 有关加密模块基础 Azure 托管磁盘的详细信息，请参阅[加密 API：下一代](/windows/desktop/seccng/cng-portal)

服务器端加密不会影响托管磁盘的性能，并且不会产生额外的费用。 

> [!NOTE]
> 临时磁盘不是托管磁盘，不会由 SSE 加密，除非在主机上启用了加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

可以依赖于平台托管的密钥来加密托管磁盘，也可以使用自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，可以指定一个客户托管密钥，用于加密和解密托管磁盘中的所有数据。 

以下部分更详细地介绍了密钥管理的每个选项。

### <a name="platform-managed-keys"></a>平台托管的密钥

默认情况下，托管磁盘使用平台托管的加密密钥。 所有写入现有托管磁盘的托管磁盘、快照、映像和数据都会自动使用平台托管密钥进行静态加密。

### <a name="customer-managed-keys"></a>客户管理的密钥

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，则必须[复制所有数据](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)到完全不同的托管磁盘（未使用客户托管密钥）。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>支持的区域

客户管理的密钥在托管磁盘可用的所有区域中都可用。

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>主机加密 - VM 数据的端到端加密

端对端加密从 VM 主机（将 VM 分配到的 Azure 服务器）开始。 临时磁盘上的数据、临时 OS 磁盘和持久化 OS/数据磁盘缓存存储在该 VM 主机上。 当你启用端到端加密时，所有这些数据都会静态加密，且数据流将加密到用于保存数据的存储服务。 端对端加密不使用 VM 的 CPU，不会影响 VM 的性能。 

启用端对端加密后，临时磁盘和临时 OS 磁盘会使用平台管理的密钥进行静态加密。 OS 和数据磁盘缓存使用客户管理的密钥或平台管理的密钥进行静态加密，具体取决于加密类型。 例如，如果使用客户管理的密钥对磁盘进行加密，则使用客户管理的密钥对磁盘的缓存进行加密，如果使用平台管理的密钥对磁盘进行加密，则使用平台管理的密钥对磁盘的缓存进行加密。

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>双静态加密

如果高安全敏感客户担心与任何特定加密算法、实现或密钥相关的风险，则现在可以选择使用平台托管的加密密钥，在基础结构层使用不同的加密算法/模式。 这一新的层可以应用于持久化的 OS 和数据磁盘、快照和映像，所有这些都将以双加密方式进行静态加密。

### <a name="supported-regions"></a>支持的区域

在托管磁盘可用的所有区域中都提供双加密。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[Azure 磁盘加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Windows 的 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 功能，通过来宾 VM 中的客户托管密钥来加密托管磁盘。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会转移到新租户，因此，客户托管密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。


## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](disks-enable-host-based-encryption-powershell.md) 或 [Azure 门户](../disks-enable-host-based-encryption-portal.md)在主机上启用端到端加密。
- 使用 [PowerShell](disks-enable-double-encryption-at-rest-powershell.md) 或 [Azure 门户](../disks-enable-double-encryption-at-rest-portal.md)为托管磁盘启用静态加密。
- 使用 [PowerShell](disks-enable-customer-managed-keys-powershell.md) 或 [Azure 门户](../disks-enable-customer-managed-keys-portal.md)为托管磁盘启用客户管理的密钥。
- [探索 Azure 资源管理器模板以使用客户管理密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
