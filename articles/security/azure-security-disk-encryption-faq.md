---
title: Azure 磁盘加密常见问题解答 | Microsoft Docs
description: 本文提供有关适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密的常见问题解答。
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: aa2278e7d32147d75b8ca0241a39a9fed6df41c8
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266876"
---
# <a name="azure-disk-encryption-faq"></a>Azure 磁盘加密 FAQ

本文提供有关适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密的常见问题解答 (FAQ)。 有关此服务的详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>哪里有正式发布版 (GA) 的 Azure 磁盘加密？

适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密已在所有 Azure 公共区域推出了正式版。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁盘加密提供哪些用户体验？

Azure 磁盘加密正式版支持 Azure 资源管理器模板、Azure PowerShell 和 Azure CLI。 以此提供了很多的灵活性。 可以通过三个不同的选项为 IaaS VM 启用磁盘加密。 有关 Azure 磁盘加密中提供的用户体验详细信息和分步指南，请参阅 [Azure 磁盘加密部署方案和体验](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁盘加密如何收费？

使用 Azure 磁盘加密来加密 VM 磁盘是免费的，但使用与 Azure Key Vault 相关联的内容则会产生费用。 有关 Azure Key Vault 成本的详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)页面。

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Azure 磁盘加密支持哪些虚拟机层？

Azure 磁盘加密可在标准层 VM 上使用，包括 [A、D、DS、G、GS 和 F](https://azure.microsoft.com/pricing/details/virtual-machines/) 系列 IaaS VM。 还可用于使用高级存储的 VM。 它不适用于基本层 VM。

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Azure 磁盘加密支持哪些 Linux 分发版？

在以下 Linux 服务器分发和版本上支持 Azure 磁盘加密：

| Linux 分发版 | 版本 | 支持加密的卷类型|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS 和数据磁盘 |
| Ubuntu | 14.04.5-DAILY-LTS | OS 和数据磁盘 |
| RHEL | 7.5 | 数据磁盘* |
| RHEL | 7.4 | 数据磁盘* |
| RHEL | 7.3 | 数据磁盘* |
| RHEL | 7.2 | 数据磁盘* |
| RHEL | 6.8 | 数据磁盘* |
| RHEL | 6.7 | 数据磁盘* |
| CentOS | 7.4 | OS 和数据磁盘 |
| CentOS | 7.3 | OS 和数据磁盘 |
| CentOS | 7.2n | OS 和数据磁盘 |
| CentOS | 6.8 | OS 和数据磁盘 |
| CentOS | 7.1 | 数据磁盘 |
| CentOS | 7.0 | 数据磁盘 |
| CentOS | 6.7 | 数据磁盘 |
| CentOS | 6.6 | 数据磁盘 |
| CentOS | 6.5 | 数据磁盘 |
| openSUSE | 13.2 | 数据磁盘 |
| SLES | 12 SP1 | 数据磁盘 |
| SLES | Priority:12-SP1 | 数据磁盘 |
| SLES | HPC 12 | 数据磁盘 |
| SLES | Priority:11-SP4 | 数据磁盘 |
| SLES | 11 SP4 | 数据磁盘 |

*__RHEL 支持将 ADE 用于数据磁盘。当前的 ADE 实现适用于 OS 磁盘，但当前并非联合支持。Microsoft 和 Red Hat 都在努力实现联合支持解决方案。在此期间，可以在[此处](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)参考用于 Linux OS 磁盘加密的 ADE 白皮书。__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何开始使用 Azure 磁盘加密？

若要开始使用，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)白皮书。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁盘加密来加密引导卷和数据卷？

是的，可以加密 Windows 和 Linux IaaS VM 的引导卷和数据卷。 对于 Windows VM，在不事先加密 OS 卷的情况下无法加密数据。 对于 Linux VM，可以在不事先加密 OS 卷的情况下加密数据卷。 加密 Linux 的 OS 卷之后，不支持针对 Linux IaaS VM 的 OS 卷禁用加密。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Azure 磁盘加密是否支持自带秘钥 (BYOK) 的功能？

是的，可以提供自己的密钥加密密钥。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密部署方案和体验](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 创建的密钥加密密钥？

是的，可以使用 Azure Key Vault 来生成密钥加密密钥供 Azure 磁盘加密使用。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密部署方案和体验](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用本地密钥管理服务或 HSM 来保护加密密钥？

无法使用本地密钥管理服务或 HSM 来配合 Azure 磁盘加密保护加密密钥。 只能使用 Azure Key Vault 服务来保护加密密钥。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密部署方案和体验](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>配置 Azure 磁盘加密的先决条件是什么？

必须要有一个 PowerShell 脚本。 通过此脚本，可以创建 Azure Active Directory 应用程序、创建新的 Key Vault 或设置现有 Key Vault 进行磁盘加密访问，以启用加密并保护机密和密钥。 有关密钥加密密钥支持方案的详细信息，请参阅 [Azure 磁盘加密先决条件和部署方案与体验](azure-security-disk-encryption.md#prerequisites)。

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>在哪里可以获得有关如何使用 PowerShell 配置 Azure 磁盘加密的详细信息？

我们发布了一些不错的文章来介绍如何执行基本的 Azure 磁盘加密任务以及更高级的方案。 对于基本任务，请参阅[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密 – 第 1 部分](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)。 对于更高级的方案，请参阅[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密 – 第 2 部分](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)。

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁盘加密支持哪些 Azure PowerShell 版本？

使用最新版的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新版本的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 版本 1.1.0 不支持 Azure 磁盘加密。

> [!NOTE]
> Linux Azure 磁盘加密预览扩展已弃用。 有关详细信息，请参阅[弃用适用于 Linux IaaS VM 的 Azure 磁盘加密预览扩展](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>是否可对自定义 Linux 映像应用 Azure 磁盘加密？

不能对自定义 Linux 映像应用 Azure 磁盘加密。 仅支持上述受支持分发版的 Linux 库映像。 目前不支持自定义 Linux 映像。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>是否可以向使用 yum 更新的 Linux Red Hat VM 应用更新？

是的，可以对 Red Hat Linux VM 执行更新或修补。 有关详细信息，请参阅[通过使用 yum 更新将更新应用于加密的 Azure IaaS Red Hat VM](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>对于 Linux，应使用哪种 Azure 磁盘加密工作流？

为在 Linux 上获得最佳结果，建议使用以下工作流：
* 从与所需的 OS 发行版和版本相对应的未修改存储库映像启动
* 备份要加密的任何已装载的驱动器。  这是为了可在失败时进行恢复，例如 VM 在加密完成前重启。
* 加密（可能需要多个小时或甚至几天时间，具体取决于 VM 的特征和任何附加数据磁盘的大小）
* 根据需要自定义软件，并将其添加到映像。

如果此工作流不可用，可在平台存储帐户层使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE)，作为通过 dm-crypt 实现完整磁盘加密的一个替代方法。

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>磁盘“Bek 卷”或“/mnt/azure_bek_disk”是什么？

“Bek 卷”（适用于 Windows）或“/mnt/azure_bek_disk”（适用于 Linux）是安全存储用于加密 Azure IaaS VM 的加密密钥的本地数据卷。
> [!NOTE]
> 请勿删除或编辑此磁盘中的任何内容。 请勿卸载磁盘，因为 IaaS VM 上的任何加密操作都需要有加密密钥才能执行。

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>可以在何处提问或提供反馈？

可在 [Azure 磁盘加密论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)上提问或提供反馈。

## <a name="next-steps"></a>后续步骤
本文档详细描述了有关 Azure 磁盘加密的最常见问题。 有关此服务及其功能的详细信息，请参阅以下文章：

- [在 Azure 安全中心应用磁盘加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [加密 Azure 虚拟机](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure 静态数据加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
