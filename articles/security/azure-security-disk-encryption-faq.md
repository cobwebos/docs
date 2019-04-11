---
title: 常见问题解答 - 适用于 IaaS VM 的 Azure 磁盘加密 | Microsoft Docs
description: 本文提供有关适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密的常见问题解答。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/05/2019
ms.custom: seodec18
ms.openlocfilehash: 413ed4a56afad5ced221ba957af9ddb007622b6a
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470043"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>适用于 IaaS VM 的 Azure 磁盘加密常见问题解答

本文提供有关适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密的常见问题解答 (FAQ)。 有关此服务的详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](azure-security-disk-encryption-overview.md)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>哪里有正式发布版 (GA) 的 Azure 磁盘加密？

适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密已在所有 Azure 公共区域推出了正式版。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁盘加密提供哪些用户体验？

Azure 磁盘加密正式版支持 Azure 资源管理器模板、Azure PowerShell 和 Azure CLI。 不同的用户体验提供了灵活性。 可以通过三个不同的选项为 IaaS VM 启用磁盘加密。 有关 Azure 磁盘加密中提供的用户体验详细信息和分步指南，请参阅[启用适用于 Windows 的 Azure 磁盘加密](azure-security-disk-encryption-windows.md)和[启用适用于 Linux 的 Azure 磁盘加密](azure-security-disk-encryption-linux.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁盘加密如何收费？

使用 Azure 磁盘加密来加密 VM 磁盘是免费的，但使用与 Azure Key Vault 相关联的内容则会产生费用。 有关 Azure Key Vault 成本的详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)页面。


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Azure 磁盘加密支持哪些虚拟机层？

Azure 磁盘加密可在标准层 VM 上使用，包括 [A、D、DS、G、GS 和 F](https://azure.microsoft.com/pricing/details/virtual-machines/) 系列 IaaS VM。 在具有高级存储的 VM 上也可用。 它在基本层 VM 上不可用。

## <a name="bkmk_LinuxOSSupport"></a>Azure 磁盘加密支持哪些 Linux 分发版？

子集上支持 azure 磁盘加密[Azure 认可的 Linux 分发版](../virtual-machines/linux/endorsed-distros.md)，这本身就是所有可能的发行版的子集。

 ![支持 Azure 磁盘加密的维恩图的 Linux 服务器分发](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Azure 不认可的发行版不支持 Azure 磁盘加密，并且这些认可，只有以下 Linux 服务器分发和版本支持 Azure 磁盘加密：

| Linux 分发版 | 版本 | 支持加密的卷类型|
| --- | --- |--- |
| Ubuntu | 16.04| OS 和数据磁盘 |
| Ubuntu | 14.04.5</br>[使用 Azure 优化内核更新为 4.15 或更高版本](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | OS 和数据磁盘 |
| RHEL | 7.6 | OS 和数据磁盘* |
| RHEL | 7.5 | OS 和数据磁盘* |
| RHEL | 7.4 | OS 和数据磁盘* |
| RHEL | 7.3 | OS 和数据磁盘* |
| RHEL | 7.2 | OS 和数据磁盘* |
| RHEL | 6.8 | 数据磁盘* |
| RHEL | 6.7 | 数据磁盘* |
| CentOS | 7.5 | OS 和数据磁盘 |
| CentOS | 7.4 | OS 和数据磁盘 |
| CentOS | 7.3 | OS 和数据磁盘 |
| CentOS | 7.2n | OS 和数据磁盘 |
| CentOS | 6.8 | OS 和数据磁盘 |
| CentOS | 7.1 | 数据磁盘 |
| CentOS | 7.0 | 数据磁盘 |
| CentOS | 6.7 | 数据磁盘 |
| CentOS | 6.6 | 数据磁盘 |
| CentOS | 6.5 | 数据磁盘 |
| openSUSE | 42.3 | 数据磁盘 |
| SLES | 12-SP4 | 数据磁盘 |
| SLES | 12-SP3 | 数据磁盘 |

> [!NOTE]
> 新的 ADE 实现支持 RHEL OS 和 RHEL7 即用即付映像的数据磁盘。 ADE 目前不支持 RHEL 自带订阅 (BYOS) 映像。 另请参阅[适用于 Linux 的 Azure 磁盘加密](azure-security-disk-encryption-linux.md)文章了解详细信息。 __

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何开始使用 Azure 磁盘加密？

若要开始，请参阅 [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁盘加密来加密引导卷和数据卷？

是的，可以加密 Windows 和 Linux IaaS VM 的引导卷和数据卷。 对于 Windows VM，在不事先加密 OS 卷的情况下无法加密数据。 对于 Linux VM，可以在不事先加密 OS 卷的情况下加密数据卷。 加密 Linux 的 OS 卷之后，不支持针对 Linux IaaS VM 的 OS 卷禁用加密。 对于在规模集中的 Linux Vm，仅数据可以对卷进行加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>可以加密 Azure 磁盘加密的卸载的卷？

否，Azure 磁盘加密仅加密已装载的卷。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何旋转秘密或加密密钥？

若要轮换机密，只需调用您最初用于启用磁盘加密在同一命令，指定不同的密钥保管库。 若要轮换的密钥加密密钥，调用您最初用于启用磁盘加密，指定新的密钥加密在同一命令。 

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如何添加或删除密钥加密密钥，如果我最初未使用其中一个？

若要添加密钥加密密钥，请调用再次传递密钥加密密钥参数启用命令。 若要删除的密钥加密密钥，请调用不使用密钥加密密钥参数再次启用命令。

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
> Linux Azure 磁盘加密预览扩展已弃用。 有关详细信息，请参阅[弃用适用于 Linux IaaS VM 的 Azure 磁盘加密预览扩展](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>是否可对自定义 Linux 映像应用 Azure 磁盘加密？

不能对自定义 Linux 映像应用 Azure 磁盘加密。 仅支持上述受支持分发版的 Linux 库映像。 目前不支持自定义 Linux 映像。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>是否可以向使用 yum 更新的 Linux Red Hat VM 应用更新？

是的您可以对 Red Hat Linux VM 执行 yum 更新。  有关详细信息，请参阅[防火墙后面的 Linux 程序包管理](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall)。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>对于 Linux，应使用哪种 Azure 磁盘加密工作流？

为在 Linux 上获得最佳结果，建议使用以下工作流：
* 从与所需的 OS 发行版和版本相对应的未修改存储库映像启动
* 备份要加密的任何已装载的驱动器。  使用此备份，在失败时能够进行恢复，例如当 VM 在加密完成前重启时。
* 加密（可能需要花费几小时甚至几天，具体取决于 VM 特征和所附加的任何数据磁盘的大小）
* 根据需要自定义软件，并将其添加到映像。

如果此工作流不可用，可在平台存储帐户层使用[存储服务加密](../storage/common/storage-service-encryption.md) (SSE)，作为通过 dm-crypt 实现完整磁盘加密的一个替代方法。

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>磁盘“Bek 卷”或“/mnt/azure_bek_disk”是什么？

“Bek 卷”（适用于 Windows）或“/mnt/azure_bek_disk”（适用于 Linux）是安全存储用于加密 Azure IaaS VM 的加密密钥的本地数据卷。
> [!NOTE]
> 请勿删除或编辑此磁盘中的任何内容。 请勿卸载磁盘，因为 IaaS VM 上的任何加密操作都需要有加密密钥才能执行。


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁盘加密使用何种加密方法？

在 Windows 上，ADE 使用 BitLocker AES256 加密方法（Windows Server 2012 之前版本上的 AES256WithDiffuser）。 在 Linux 上，ADE 使用 256 位卷主密钥使用 aes xts plain64 解密默认值。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll 并指定了所有卷类型，它是否会擦除我们已加密的数据驱动器上的数据？
否，不会擦除已使用 Azure 磁盘加密进行了加密的数据驱动器上的数据。 与 EncryptFormatAll 不重新加密 OS 驱动器类似，它也不会重新加密已加密的数据驱动器。 有关详细信息，请参阅 [EncryptFormatAll 条件](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)。        

## <a name="is-xfs-filesystem-supported"></a>是否支持 XFS 文件系统？
数据磁盘加密仅使用 EncryptFormalAll 支持 XFS 卷。 这将重新格式化该卷，清除以前在那里任何数据。 有关详细信息，请参阅 [EncryptFormatAll 条件](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>可以备份和还原已加密的 VM？ 

Azure 备份提供了一种机制来备份和还原加密的 VM 的同一订阅和区域中。  有关说明，请参阅[备份和还原加密的虚拟机与 Azure 备份](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption)。  当前不支持将已加密的 VM 还原到不同的区域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>可以在何处提问或提供反馈？

可在 [Azure 磁盘加密论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)上提问或提供反馈。

## <a name="next-steps"></a>后续步骤
本文档详细描述了有关 Azure 磁盘加密的最常见问题。 有关此服务的详细信息，请参阅以下文章：

- [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)
- [在 Azure 安全中心中应用磁盘加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [静态 azure 数据加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
