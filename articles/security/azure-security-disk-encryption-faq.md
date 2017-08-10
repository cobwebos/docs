---
title: "Azure 磁盘加密常见问题解答 | Microsoft Docs"
description: "本文提供适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密的常见问题解答。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 2a81a88750ae40cd75e5d394f97c8310416546e4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>Azure 磁盘加密常见问题解答 (FAQ)

本“常见问题解答”文章解答有关适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密的问题；有关此服务的详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)。

## <a name="general-questions"></a>一般问题
问： Azure 磁盘加密在哪些区域推出了正式版 (GA)？
答：适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密已在所有 Azure 公共区域推出了正式版。

问：Azure 磁盘加密提供哪些用户体验？
答：Azure 磁盘加密正式版支持 Azure 资源管理器模板、Azure PowerShell 和 Azure CLI。 因此它提供很高的灵活性，可让我们通过三个不同的选项为 IaaS VM 启用磁盘加密。 “Azure 磁盘加密部署方案和体验”中提供了有关用户体验的更多详细信息和分步指南。

问：Azure 磁盘加密如何收费？
答：可以免费使用 Azure 磁盘加密来加密 VM 磁盘。

问：可对哪些虚拟机层使用 Azure 磁盘加密？
答：Azure 磁盘加密仅适用于标准层 VM，包括 [A、D、DS、G、GS、F](https://azure.microsoft.com/pricing/details/virtual-machines/) 等系列的 IaaS VM，以及使用高级存储的 VM。 它不适用于基本层 VM。

问：Azure 磁盘加密支持哪些 Linux 分发版？
答：以下 Linux 服务器分发版和版本支持 Azure 磁盘加密：
| Linux 分发 | 版本 | 支持加密的卷类型|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS 和数据磁盘 |
| Ubuntu | 14.04.5-DAILY-LTS | OS 和数据磁盘 |
| RHEL | 7.3 | OS 和数据磁盘 |
| RHEL | 7.2 | OS 和数据磁盘 |
| RHEL | 6.8 | OS 和数据磁盘 |
| RHEL | 6.7 | 数据磁盘 |
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

问：如何开始使用 Azure 磁盘加密？
答：客户可以阅读[此处](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)的 Azure 磁盘加密白皮书来了解如何开始使用

问：是否可以使用 Azure 磁盘加密来加密引导卷和数据卷？
答：是的，可以加密 Windows 和 Linux IaaS VM 的引导卷和数据卷。 对于 Windows VM，在不事先加密 OS 卷的情况下无法加密数据。 对于 Linux VM，可以在不事先加密 OS 卷的情况下加密数据卷。 加密 Linux 的 OS 卷之后，不支持针对 Linux IaaS VM 的 OS 卷禁用加密

问：Azure 磁盘加密是否支持“自带密钥”(BYOK) 功能？
答：是的，可以提供自己的密钥加密密钥。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥支持方案的更多详细信息，请参阅“Azure 磁盘加密部署方案和体验”

问：是否可以使用 Azure 创建的密钥加密密钥？
答：是的，可以使用 Azure Key Vault 来生成密钥加密密钥供 Azure 磁盘加密使用。 这些密钥在 Azure Key Vault（Azure 磁盘加密的密钥存储）中受保护。 有关密钥加密密钥支持方案的更多详细信息，请参阅“Azure 磁盘加密部署方案和体验”

问：是否可以使用本地密钥管理服务/HSM 来保护加密密钥？
答：无法使用本地密钥管理服务/HSM 来配合 Azure 磁盘加密保护加密密钥。 只能使用 Azure Key Vault 服务保护加密密钥。 有关密钥加密密钥支持方案的更多详细信息，请参阅“Azure 磁盘加密部署方案和体验”

问：配置 Azure 磁盘加密的先决条件是什么？
答：Azure 磁盘加密先决条件：必须使用 PowerShell 脚本创建 AAD 应用程序、创建新的 Key Vault 或设置现有 Key Vault 进行磁盘加密访问，以启用加密并保护机密和密钥。  有关密钥加密密钥支持方案的更多详细信息，请参阅“Azure 磁盘加密先决条件和部署方案与体验”

问：在哪里可以获得有关如何使用 PowerShell 配置 Azure 磁盘加密的详细信息？
答：我们发布了一些不错的文章来介绍如何执行基本的 Azure 磁盘加密任务以及更高级的方案。 对于基本任务，请查看[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 1 部分](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)。 对于更高级的方案，请查看[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 2 部分](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)。

问：Azure 磁盘加密支持哪些 Azure PowerShell 版本？
答：使用最新版本的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新版本的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 版本 1.1.0 不支持 Azure 磁盘加密。

> [!NOTE]
> Linux Azure 磁盘加密预览扩展已弃用。 有关详细信息，请参阅[此文档](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)

问：是否可对自定义 Linux 映像应用 Azure 磁盘加密？
答：不能对自定义 Linux 映像应用 Azure 磁盘加密。 仅支持上述受支持分发版的 Linux 库映像。 目前不支持自定义 Linux 映像

问：是否可以使用 Yum 更新向 Linux Red Hat VM 应用更新？
答：是的，可以遵照[此处](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)所述的指导来更新和/或修补 Red Hat Linnux VM

问：在何处可以提问或提供反馈？答：可在 [Azure 磁盘加密论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption)上提问或提供反馈

## <a name="see-also"></a>另请参阅
本文档详细描述了有关 Azure 磁盘加密的最常见问题；有关此服务及其功能的详细信息，请参阅：

- [在 Azure 安全中心应用磁盘加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [加密 Azure 虚拟机](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure 静态数据加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

