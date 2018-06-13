---
title: 在 Azure 安全中心中应用磁盘加密 | Microsoft 文档
description: 本文档演示如何实现 Azure 安全中心建议**应用磁盘加密**。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23040212"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>在 Azure 安全中心中应用磁盘加密
Azure 安全中心建议：如有未使用 Azure 磁盘加密加密的 Windows 或 Linux VM 磁盘，则应用磁盘加密。 通过磁盘加密，可以对 Windows 和 Linux IaaS VM 磁盘进行加密。  对于 VM 上的 OS 和数据卷，建议使用加密。

磁盘加密使用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能。 这些功能提供 OS 和数据加密来帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 磁盘加密与 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 集成，可帮助你控制和管理密钥保管库订阅中的磁盘加密密钥和机密，同时确保 VM 磁盘中的所有数据可在 [Azure 存储](https://azure.microsoft.com/documentation/services/storage/)中静态加密。

> [!NOTE]
> 以下 Windows 服务器操作系统支持 Azure 磁盘加密 - Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2。 以下 Linux 服务器操作系统支持磁盘加密 - Ubuntu、CentOS、SUSE 和 SUSE Linux Enterprise Server (SLES)。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“应用磁盘加密”。
2. 在“应用磁盘加密”边栏选项卡中，将看到一组建议应用磁盘加密的 VM。
3. 按照说明操作，对这些 VM 应用加密。

![][1]

若要加密已被安全中心标识为需要加密的 Azure 虚拟机，建议执行以下步骤：

* 安装和配置 Azure PowerShell。 此步骤用于运行设置先决条件所需的 PowerShell 命令，这些先决条件是加密 Azure 虚拟机所必需的。
* 获取并运行 Azure 磁盘加密先决条件 Azure PowerShell 脚本。
* 加密虚拟机。

[加密 Azure 虚拟机](security-center-disk-encryption.md)指导完成这些步骤。  本主题假定用户使用 Windows 10 作为从中配置磁盘加密的客户端计算机。

有许多方法可用于 Azure 虚拟机。 如果用户已熟知 Azure PowerShell 或 Azure CLI，则用户可能更愿意使用备用方式。 若要了解这些其他方法，请参阅 [Azure 磁盘加密](../security/azure-security-disk-encryption.md)。

## <a name="see-also"></a>另请参阅
本文档演示了如何实现安全中心建议“应用磁盘加密”。 若要了解有关磁盘加密的详细信息，请参阅以下文章：

* [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/)（Azure 密钥保管库的加密和密钥管理）（视频，36 分 39 秒）- 了解如何将磁盘加密管理用于 IaaS VM 和 Azure 密钥保管库以帮助保护数据。
* [加密 Azure 虚拟机](security-center-disk-encryption.md)（文档）- 了解如何对 Azure 虚拟机进行加密。
* [Azure 磁盘加密](../security/azure-security-disk-encryption.md)（文档）- 了解如何对 Windows 和 Linux VM 启用磁盘加密。

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](security-center-policies.md) - 了解如何配置安全策略。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
