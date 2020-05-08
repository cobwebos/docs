---
title: FAQ-适用于 Windows Vm 的 Azure 磁盘加密
description: 本文提供了有关 Microsoft Azure Windows IaaS Vm 的磁盘加密的常见问题的解答。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 61de52e5a6703682d52d49efe9decb814231dae4
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901287"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>适用于 Windows 虚拟机的 Azure 磁盘加密常见问题解答

本文提供有关适用于 Windows VM 的 Azure 磁盘加密的常见问题解答 (FAQ)。 有关此服务的详细信息，请参阅 [Azure 磁盘加密概述](disk-encryption-overview.md)。

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>什么是适用于 Windows Vm 的 Azure 磁盘加密？

适用于 Windows Vm 的 Azure 磁盘加密使用 Windows 的 BitLocker 功能提供 OS 磁盘和数据磁盘的完整磁盘加密。 此外，当[将 volumetype 参数为 All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk)时，它提供临时磁盘的加密。  内容以加密的形式从 VM 流向存储后端。 因此，使用客户托管密钥提供端对端加密。
 
请参阅[支持的 VM 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)。
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>哪里有正式发布版 (GA) 的 Azure 磁盘加密？

Azure 磁盘加密已在所有 Azure 公共区域推出了正式版。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁盘加密提供哪些用户体验？

Azure 磁盘加密正式版支持 Azure 资源管理器模板、Azure PowerShell 和 Azure CLI。 不同的用户体验提供了灵活性。 可以通过三个不同的选项为 VM 启用磁盘加密。 有关 Azure 磁盘加密中提供的用户体验详细信息和分步指南，请参阅[适用于 Windows 的 Azure 磁盘加密方案](disk-encryption-windows.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁盘加密如何收费？

使用 Azure 磁盘加密来加密 VM 磁盘是免费的，但使用与 Azure Key Vault 相关联的内容则会产生费用。 有关 Azure Key Vault 成本的详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)页面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何开始使用 Azure 磁盘加密？

若要开始，请参阅 [Azure 磁盘加密概述](disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>哪些 VM 大小和操作系统支持 Azure 磁盘加密？

[Azure 磁盘加密概述](disk-encryption-overview.md)一文列出了支持 Azure 磁盘加密的 [VM 大小](disk-encryption-overview.md#supported-vms)和 [VM 操作系统](disk-encryption-overview.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁盘加密来加密引导卷和数据卷？

可以加密启动卷和数据卷，但若未事先加密 OS 卷，则无法加密数据。

加密 OS 卷之后，不支持在 OS 卷上禁用加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>是否可以使用 Azure 磁盘加密来加密未装载的卷？

否。Azure 磁盘加密只能加密已装载的卷。

## <a name="what-is-storage-server-side-encryption"></a>什么是存储服务器端加密？

存储服务器端加密会在 Azure 存储中加密 Azure 托管磁盘。 默认情况下，托管磁盘使用平台托管密钥通过服务器端加密进行加密（从 2017 年 6 月 10 日开始）。 可以通过指定客户托管密钥，使用自己的密钥来管理托管磁盘加密。 有关详细信息，请参阅[Azure 托管磁盘的服务器端加密](disk-encryption.md)。
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Azure 磁盘加密与使用客户管理的密钥进行存储服务器端加密的方式不同，何时应使用每个解决方案？

Azure 磁盘加密使用客户管理的密钥为 OS 磁盘、数据磁盘和临时磁盘提供端对端加密。

- 如果你的要求包括对上述和端到端加密的加密，请使用 Azure 磁盘加密。 
- 如果你的要求包括仅使用客户管理的密钥加密静态数据，则对[客户管理的密钥使用服务器端加密](disk-encryption.md)。 不能使用 Azure 磁盘加密和使用客户托管密钥的存储服务器端加密来加密磁盘。
- 如果在[不受支持的 Windows 方案](disk-encryption-windows.md#unsupported-scenarios)中使用称为的方案，请考虑[使用客户管理的密钥进行服务器端加密](disk-encryption.md)。 
- 如果你的组织的策略允许使用 Azure 托管的密钥加密静态内容，则无需执行任何操作-默认情况下会对内容进行加密。 对于托管磁盘，默认情况下，使用平台托管密钥的服务器端加密对存储中的内容进行加密。 密钥由 Azure 存储服务管理。 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何轮换机密或加密密钥？

若要轮换机密，只需调用你一开始在启用磁盘加密时使用的命令并指定另一 Key Vault 即可。 若要轮换密钥加密密钥，只需调用你一开始在启用磁盘加密时使用的命令并指定新的密钥加密方法即可。 

>[!WARNING]
> - 如果之前是通过指定 Azure AD 凭据使用 [Azure 磁盘加密与 Azure AD 应用](disk-encryption-windows-aad.md)选项来加密此 VM，则必须继续使用此选项来加密 VM。 无法在此加密的 VM 上使用 Azure 磁盘加密，因为不支持此方案，这意味着尚不支持为此加密的 VM 实施 AAD 应用程序切换操作。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如何在一开始并没有使用密钥加密密钥的情况下添加或删除该密钥？

若要添加密钥加密密钥，请再次调用 enable 命令，传递密钥加密密钥参数。 若要删除密钥加密密钥，请在没有密钥加密密钥参数的情况下再次调用 enable 命令。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁盘加密是否支持自带秘钥 (BYOK)？

是的，可以提供自己的密钥加密密钥。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥支持方案的详细信息，请参阅[创建和配置用于 Azure 磁盘加密的 Key Vault](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 创建的密钥加密密钥？

是的，可以使用 Azure Key Vault 来生成密钥加密密钥供 Azure 磁盘加密使用。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥的详细信息，请参阅[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用本地密钥管理服务或 HSM 来保护加密密钥？

无法使用本地密钥管理服务或 HSM 来配合 Azure 磁盘加密保护加密密钥。 只能使用 Azure Key Vault 服务来保护加密密钥。 有关密钥加密密钥支持方案的详细信息，请参阅[创建和配置用于 Azure 磁盘加密的 Key Vault](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>配置 Azure 磁盘加密的先决条件是什么？

Azure 磁盘加密具有先决条件。 若要创建新的 Key Vault 或设置现有 Key Vault 进行磁盘加密访问，以启用加密并保护机密和密钥，请参阅[创建和配置用于 Azure 磁盘加密的 Key Vault](disk-encryption-key-vault.md)一文。 有关密钥加密密钥支持方案的详细信息，请参阅[创建和配置用于 Azure 磁盘加密的 Key Vault](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 应用（早期版本）配置 Azure 磁盘加密的先决条件是什么？

Azure 磁盘加密具有先决条件。 请参阅[使用 Azure AD 的 Azure 磁盘加密](disk-encryption-windows-aad.md)内容，创建 Azure Active Directory 应用程序、创建新的 Key Vault 或设置现有 Key Vault 进行磁盘加密访问，以启用加密并保护机密和密钥。 有关密钥加密密钥支持方案的详细信息，请参阅[创建和配置可将 Azure 磁盘加密和 Azure AD 配合使用的 Key Vault](disk-encryption-key-vault-aad.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍然支持使用 Azure AD 应用（早期版本）进行 Azure 磁盘加密？
是的。 仍然支持使用 Azure AD 应用进行磁盘加密。 不过，当加密新的 VM 时，建议使用新方法而不是使用 Azure AD 应用进行加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以在不使用 Azure AD 应用的情况下将通过 Azure AD 应用加密的 VM 迁移到此加密？
  当前，对于通过 Azure AD 应用加密的计算机，没有直接迁移路径可用来在不使用 Azure AD 应用的情况下迁移到此加密。 此外，也没有直接路径用来将未使用 Azure AD 应用的加密迁移到使用 AD 应用的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁盘加密支持哪些 Azure PowerShell 版本？

使用最新版的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新版本的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 版本 1.1.0 不  支持 Azure 磁盘加密。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>磁盘“Bek 卷”或“/mnt/azure_bek_disk”是什么？

“Bek 卷”是一个本地数据卷，可以安全地存储用于已加密 Azure VM 的加密密钥。

> [!NOTE]
> 请勿删除或编辑此磁盘中的任何内容。 请勿卸载磁盘，因为 IaaS VM 上的任何加密操作都需要有加密密钥才能执行。

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁盘加密使用何种加密方法？

Azure 磁盘加密基于 Windows 版本选择 BitLocker 中的加密方法，如下所示：

| Windows 版本                 | 版本 | 加密方法        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012、Windows 10 或更高版本  | >= 1511 |XTS-AES 256 位           |
| Windows Server 2012、Windows 8、8.1、10 | < 1511 |AES 256 位 *              |
| Windows Server 2008R2            |        |带有扩散器的 AES 256 位 |

\*Windows 2012 和更高版本不支持带有扩散器的 AES 256 位。

若要确定 Windows OS 版本，请在虚拟机中运行 "winver" 工具。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>能否备份和还原加密的 VM？ 

Azure 备份提供一个机制，可以用来备份和还原同一订阅与区域中的已加密 VM。  有关说明，请参阅[使用 Azure 备份来备份和还原已加密的虚拟机](../../backup/backup-azure-vms-encryption.md)。  目前不支持将已加密的 VM 还原到另一区域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>可以在何处提问或提供反馈？

可在 [Azure 磁盘加密论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)上提问或提供反馈。

## <a name="next-steps"></a>后续步骤
本文档详细描述了有关 Azure 磁盘加密的最常见问题。 有关此服务的详细信息，请参阅以下文章：

- [Azure 磁盘加密概述](disk-encryption-overview.md)
- [在 Azure 安全中心应用磁盘加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure 静态数据加密](../../security/fundamentals/encryption-atrest.md)
