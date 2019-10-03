---
title: 常见问题解答 - 适用于 IaaS VM 的 Azure 磁盘加密 | Microsoft Docs
description: 本文提供有关适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密的常见问题解答。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 4f2a34e63a870814c8d2a3ffe24c60083c9d7bb2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781109"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>适用于 IaaS VM 的 Azure 磁盘加密常见问题解答

本文提供有关适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密的常见问题解答 (FAQ)。 有关此服务的详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](azure-security-disk-encryption-overview.md)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>哪里有正式发布版 (GA) 的 Azure 磁盘加密？

适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密已在所有 Azure 公共区域推出了正式版。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁盘加密提供哪些用户体验？

Azure 磁盘加密正式版支持 Azure 资源管理器模板、Azure PowerShell 和 Azure CLI。 不同的用户体验提供了灵活性。 可以通过三个不同的选项为 IaaS VM 启用磁盘加密。 有关 Azure 磁盘加密中提供的用户体验详细信息和分步指南，请参阅[启用适用于 Windows 的 Azure 磁盘加密](azure-security-disk-encryption-windows.md)和[启用适用于 Linux 的 Azure 磁盘加密](azure-security-disk-encryption-linux.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁盘加密如何收费？

使用 Azure 磁盘加密来加密 VM 磁盘是免费的，但使用与 Azure Key Vault 相关联的内容则会产生费用。 有关 Azure Key Vault 成本的详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)页面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何开始使用 Azure 磁盘加密？

若要开始，请参阅 [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>什么 VM 大小和操作系统支持 Azure 磁盘加密？

[Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)一文列出了支持 Azure 磁盘加密的[Vm 大小](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes)和[vm 操作系统](azure-security-disk-encryption-prerequisites.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁盘加密来加密引导卷和数据卷？

是的，可以加密 Windows 和 Linux IaaS VM 的引导卷和数据卷。 对于 Windows VM，在不事先加密 OS 卷的情况下无法加密数据。 对于 Linux VM，可以在不事先加密 OS 卷的情况下加密数据卷。 加密 Linux 的 OS 卷之后，不支持针对 Linux IaaS VM 的 OS 卷禁用加密。 对于规模集中的 Linux Vm, 只能对数据卷进行加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>是否可以使用 Azure 磁盘加密来加密未装入的卷？

不, Azure 磁盘加密仅加密已装入的卷。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何实现轮换机密或加密密钥吗？

若要轮换机密, 只需调用你最初使用的相同命令启用磁盘加密, 并指定不同的 Key Vault。 若要轮换密钥加密密钥, 请调用与最初用于启用磁盘加密相同的命令, 指定新的密钥加密。 

>[!WARNING]
> - 如果以前通过指定 Azure AD 凭据加密此 VM, 将[Azure 磁盘加密用于 Azure AD 应用程序](azure-security-disk-encryption-prerequisites-aad.md), 则必须继续使用此选项来加密 vm。 无法在此加密的 VM 上使用 [Azure 磁盘加密](azure-security-disk-encryption-prerequisites.md)，因为不支持此方案，这意味着尚不支持为此加密的 VM 实施 AAD 应用程序切换操作。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如果我最初不使用密钥加密密钥, 如何实现添加或删除密钥加密密钥？

若要添加密钥加密密钥, 请再次调用 enable 命令, 同时传递密钥加密密钥参数。 若要删除密钥加密密钥, 请在不使用密钥加密密钥参数的情况下再次调用 enable 命令。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁盘加密是否支持自带秘钥 (BYOK)？

是的，可以提供自己的密钥加密密钥。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 创建的密钥加密密钥？

是的，可以使用 Azure Key Vault 来生成密钥加密密钥供 Azure 磁盘加密使用。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥的详细信息，请参阅 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用本地密钥管理服务或 HSM 来保护加密密钥？

无法使用本地密钥管理服务或 HSM 来配合 Azure 磁盘加密保护加密密钥。 只能使用 Azure Key Vault 服务来保护加密密钥。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>配置 Azure 磁盘加密的先决条件是什么？

Azure 磁盘加密具有先决条件。 若要创建新的 Key Vault 或设置现有 Key Vault 进行磁盘加密访问，以启用加密并保护机密和密钥，请参阅 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)一文。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 应用（早期版本）配置 Azure 磁盘加密的先决条件是什么？

Azure 磁盘加密具有先决条件。 请参阅 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites-aad.md)一文，创建 Azure Active Directory 应用程序、创建新的 Key Vault 或设置现有 Key Vault 进行磁盘加密访问，以启用加密并保护机密和密钥。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍然支持使用 Azure AD 应用（早期版本）进行 Azure 磁盘加密？
是的。 仍然支持使用 Azure AD 应用进行磁盘加密。 不过，当加密新的 VM 时，建议使用新方法而不是使用 Azure AD 应用进行加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以在不使用 Azure AD 应用的情况下将通过 Azure AD 应用加密的 VM 迁移到此加密？
  当前，对于通过 Azure AD 应用加密的计算机，没有直接迁移路径可用来在不使用 Azure AD 应用的情况下迁移到此加密。 此外，也没有直接路径用来将未使用 Azure AD 应用的加密迁移到使用 AD 应用的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁盘加密支持哪些 Azure PowerShell 版本？

使用最新版的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新版本的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 版本 1.1.0 不支持 Azure 磁盘加密。

> [!NOTE]
> Linux Azure 磁盘加密预览扩展 "OSTCExtension. AzureDiskEncryptionForLinux" 已弃用。 此扩展已为 Azure 磁盘加密预览版本发布。 不应在测试或生产部署中使用该扩展的预览版本。

> 对于 Azure 资源管理器 (ARM) 等部署方案, 需要部署适用于 Linux VM 的 Azure 磁盘加密扩展才能在 Linux IaaS VM 上启用加密, 则必须使用 Azure 磁盘加密生产支持的扩展AzureDiskEncryptionForLinux "(安全)。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>是否可对自定义 Linux 映像应用 Azure 磁盘加密？

不能对自定义 Linux 映像应用 Azure 磁盘加密。 仅支持上述受支持分发版的 Linux 库映像。 目前不支持自定义 Linux 映像。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>是否可以向使用 yum 更新的 Linux Red Hat VM 应用更新？

是的, 可以在 Red Hat Linux VM 上执行 yum 更新。  有关详细信息, 请参阅[防火墙后面的 Linux 包管理](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall)。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>对于 Linux，应使用哪种 Azure 磁盘加密工作流？

为在 Linux 上获得最佳结果，建议使用以下工作流：
* 从与所需的 OS 发行版和版本相对应的未修改存储库映像启动
* 备份要加密的任何已装载的驱动器。  使用此备份，在失败时能够进行恢复，例如当 VM 在加密完成前重启时。
* 加密（可能需要花费几小时甚至几天，具体取决于 VM 特征和所附加的任何数据磁盘的大小）
* 根据需要自定义软件，并将其添加到映像。

如果此工作流不可用，可在平台存储帐户层使用[存储服务加密](../storage/common/storage-service-encryption.md) (SSE)，作为通过 dm-crypt 实现完整磁盘加密的一个替代方法。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>磁盘“Bek 卷”或“/mnt/azure_bek_disk”是什么？

“Bek 卷”（适用于 Windows）或“/mnt/azure_bek_disk”（适用于 Linux）是安全存储用于加密 Azure IaaS VM 的加密密钥的本地数据卷。
> [!NOTE]
> 请勿删除或编辑此磁盘中的任何内容。 请勿卸载磁盘，因为 IaaS VM 上的任何加密操作都需要有加密密钥才能执行。


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁盘加密使用何种加密方法？

在 Windows 上，ADE 使用 BitLocker AES256 加密方法（Windows Server 2012 之前版本上的 AES256WithDiffuser）。 在 Linux 上, ADE 将 xts-plain64 的解密默认值用于256位卷主密钥。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll 并指定了所有卷类型，它是否会擦除我们已加密的数据驱动器上的数据？
否，不会擦除已使用 Azure 磁盘加密进行了加密的数据驱动器上的数据。 与 EncryptFormatAll 不重新加密 OS 驱动器类似，它也不会重新加密已加密的数据驱动器。 有关详细信息，请参阅 [EncryptFormatAll 条件](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)。        

## <a name="is-xfs-filesystem-supported"></a>是否支持 XFS filesystem？
仅对 EncryptFormatAll 的数据磁盘加密支持 XFS 卷。 这将重新格式化卷, 并清除以前的任何数据。 有关详细信息，请参阅 [EncryptFormatAll 条件](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>是否可以备份和还原已加密的 VM？ 

Azure 备份提供了一种机制, 用于在同一订阅和区域中备份和还原加密的 VM。  有关说明, 请参阅[利用 Azure 备份来备份和还原加密的虚拟机](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。  当前不支持将加密的 VM 还原到其他区域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>可以在何处提问或提供反馈？

可在 [Azure 磁盘加密论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)上提问或提供反馈。

## <a name="next-steps"></a>后续步骤
本文档详细描述了有关 Azure 磁盘加密的最常见问题。 有关此服务的详细信息，请参阅以下文章：

- [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)
- [在 Azure 安全中心应用磁盘加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure 静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
