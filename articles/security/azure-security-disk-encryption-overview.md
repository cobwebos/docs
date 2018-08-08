---
title: 适用于 IaaS VM 的 Azure 磁盘加密概述 | Microsoft Docs
description: 本文概述适用于 IaaS VM 的 Microsoft Azure 磁盘加密。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 0e81a48c1215e8590f90c42aee0861e6fda3db8e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392724"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>适用于 IaaS VM 的 Azure 磁盘加密 
Microsoft Azure 致力于确保数据隐私权和数据所有权，通过各种先进技术来加密、控制和管理加密密钥，以及控制和审核对数据的访问，让用户能够控制 Azure 托管的数据。 此控制措施使 Azure 客户能够灵活选择最符合其业务需求的解决方案。 本文介绍技术解决方案“适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密”，以帮助你保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>概述
Azure 磁盘加密 (ADE) 是用于帮助加密 Windows 和 Linux IaaS 虚拟机磁盘的功能。 ADE 利用 Windows 的行业标准 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为 OS 和数据磁盘提供卷加密。 此解决方案与 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 集成，用于控制和管理磁盘加密密钥与机密。 此解决方案还可确保虚拟机磁盘上的所有数据在 Azure 存储中静态加密。

在标准 VM 和使用高级存储的 VM 的所有 Azure 公共区域和 AzureGov 区域中，适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密以**正式版**提供。 Azure 磁盘加密管理解决方案可以解决以下业务需求：

* 使用行业标准的加密技术轻松保护 IaaS VM，满足组织的安全性与合规性要求。
* IaaS VM 会根据客户控制的密钥和策略启动，客户可以在 Key Vault 中审核密钥和策略的使用方式。

> [!NOTE]
> 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。


## <a name="encryption-scenarios"></a>加密方案
Azure 磁盘加密解决方案支持以下客户方案：

* 在通过预加密 VHD 和加密密钥创建的新 Windows IaaS VM 上启用加密 
* 在通过支持的 Azure 库映像创建的新 IaaS VM 上启用加密
* 在 Azure 中运行的现有 IaaS VM 上启用加密
* 在 Windows IaaS VM 上禁用加密
* 在 Linux IaaS VM 的数据驱动器上禁用加密
* 启用托管磁盘 VM 的加密
* 更新现有加密的高级和非高级存储 VM 的加密设置
* 备份和还原加密的 VM

在 Microsoft Azure 中启用 IaaS VM 时，该解决方案支持以下 IaaS VM 方案：

* 与 Azure Key Vault 集成
* 标准层 VM：[A、D、DS、G、GS 和 F 等系列 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * 这些层中的 [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 必须满足最小 7GB 内存要求
* 通过支持的 Azure 库映像在 Windows 和 Linux IaaS VM 和托管磁盘 VM上启用加密
* 在 Windows IaaS VM 和托管磁盘 VM 的 OS 和数据驱动器上禁用加密
* 在 Linux IaaS VM 和托管磁盘 VM 的数据驱动器上禁用加密
* 在运行 Windows 客户端 OS 的 IaaS VM 上启用加密
* 在包含装入路径的卷上启用加密
* 在使用 mdadm 配置了磁盘分段 (RAID) 的 Linux VM 上启用加密
* 使用 LVM 对 Linux VM 上的数据磁盘启用加密
* 在 Linux VM OS 和数据磁盘上启用加密 
* 在配置有存储空间的 Windows VM 上启用加密
* 更新现有加密的高级和非高级存储 VM 的加密设置
* 针对非 KEK 和 KEK 方案（KEK - 关键加密密钥），备份和还原加密的 VM
* 支持所有 Azure 公共和 AzureGov 区域

该解决方案不支持以下方案、功能和技术：

* 基本层 IaaS VM
* 在 Linux IaaS VM 的 OS 驱动器上禁用加密
* 在为 Linux Iaas VM 加密了 OS 驱动器的情况下禁止对数据驱动器加密
* 使用经典 VM 创建方法创建的 IaaS VM
* 不支持对 Linux IaaS VM 客户自定义映像启用加密。
* 与本地密钥管理服务集成
* Azure 文件（文件共享系统）、网络文件系统 (NFS)、动态卷，以及配置了基于软件的 RAID 系统的 Windows VM

## <a name="encryption-features"></a>加密功能
为 Azure IaaS VM 启用并部署 Azure 磁盘加密后，可根据提供的配置启用以下功能：

* 加密 OS 卷以轻松保护存储中的引导卷
* 加密数据卷以轻松保护存储中的数据卷
* 在 Windows IaaS VM 的 OS 和数据驱动器上禁用加密
* 在 Linux IaaS VM 的数据驱动器上禁用加密（仅当 OS 驱动器未加密时）
* 保护 Key Vault 订阅中的加密密钥和机密
* 报告已加密 IaaS VM 的加密状态
* 从 IaaS 虚拟机中删除磁盘加密配置设置
* 使用 Azure 备份服务来备份和还原已加密 VM

适用于 Windows 和 Linux 解决方案的 IaaS VM 的 Azure 磁盘加密包括：

* 适用于 Windows 的磁盘加密扩展。
* 适用于 Linux 的磁盘加密扩展。
* 磁盘加密 PowerShell cmdlet。
* 磁盘加密 Azure 命令行接口 (CLI) cmdlet。
* 磁盘加密 Azure 资源管理器模板。

运行 Windows 或 Linux OS 的 IaaS VM 支持 Azure 磁盘加密解决方案。 有关支持的操作系统的详细信息，请参阅[先决条件](azure-security-disk-encryption-prerequisites.md)一文。

> [!NOTE]
> 使用 Azure 磁盘加密加密 VM 磁盘不会产生额外的费用。 标准 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)适用于用来存储加密密钥的 Key Vault。 

## <a name="encryption-workflow"></a>加密工作流
若要为 Windows 和 Linux VM 启用磁盘加密，请执行以下步骤：

1. 从之前的加密方案中选择一种加密方案。
2. 选择通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 CLI 命令启用磁盘加密，并指定加密配置。

   * 对于客户加密的 VHD 方案，将加密的 VHD 上传到存储帐户，并将加密密钥材料上传到 Key Vault。 然后，提供加密配置，在新的 IaaS VM 上启用加密。
   * 针对通过市场创建的新 VM 和已在 Azure 中运行的现有 VM，提供加密配置以便在 IaaS VM 上启用加密。

3. 向 Azure 平台授予访问权限，使其能够从 Key Vault 中读取加密密钥数据（Windows 系统的 BitLocker 加密密钥和 Linux 密码），从而在 IaaS VM 上启用加密。

4. 提供 Azure Active Directory (Azure AD) 应用程序标识，将加密密钥材料写入 Key Vault。 这样做，即可在步骤 2 所述方案中的 IaaS VM 上启用加密。

5. Azure 使用加密和 Key Vault 配置更新 VM 服务模型，并设置加密的 VM。

 ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>解密工作流
若要为 IaaS VM 禁用磁盘加密，请完成以下高级步骤：

1. 选择在 Azure 中运行的 IaaS VM 上禁用加密（解密），并指定解密配置。 可以通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 Azure CLI 禁用加密。

 此步骤将对正在运行的 Windows IaaS VM 禁用 OS 和/或数据卷加密。 但是如前面部分所述，不支持对 Linux 禁用 OS 磁盘加密。 只要 OS 磁盘未加密，仅允许对 Linux VM 上的数据驱动器执行解密步骤。
2. Azure 更新 VM 服务模型后，IaaS VM 会被标记为已解密。 VM 的内容不再静态加密。

> [!NOTE]
> 禁用加密操作不会删除 Key Vault 和加密密钥材料（Windows 的 BitLocker 加密密钥或 Linux 密码）。
 > 不支持禁用 Linux 的 OS 磁盘加密。 仅允许对 Linux VM 上的数据驱动器执行解密步骤。
如果 OS 驱动器已加密，则不支持对 Linux 禁用数据磁盘加密。

## <a name="terminology"></a>术语
若要理解该技术所用的一些常见术语，请参考下面的术语表：

| 术语 | 定义 |
| --- | --- |
| Azure AD | Azure AD 是 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 的缩写。 使用 Azure AD 帐户进行身份验证、存储，以及从 Key Vault 检索机密。 |
| Azure 密钥保管库 | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块，可以帮助保护加密密钥和敏感机密。 有关详细信息，请参阅 [Key Vault](https://azure.microsoft.com/services/key-vault/) 文档。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 是一种行业认可的 Windows 卷加密技术，用于在 Windows IaaS VM 上启用磁盘加密。 |
| BEK | BitLocker 加密密钥用于加密 OS 引导卷和数据卷。 BitLocker 密钥在 Key Vault 中以机密形式进行保护。 |
| CLI | 请参阅 [Azure 命令行接口](/cli/azure/install-azure-cli)。|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 是基于 Linux 的透明磁盘加密子系统，用于在 Linux IaaS VM 上启用磁盘加密。 |
| KEK | 密钥加密密钥是非对称密钥 (RSA 2048)，用于在需要时保护或包装机密。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档。 |
| PS cmdlet | 请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)
