---
title: 什么是 Azure 磁盘加密？
description: 本文提供 Azure 磁盘加密的概述
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9b00a5262b1e144aa47cd7fd640906225ff4fecd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068787"
---
# <a name="azure-disk-encryption-overview"></a>Azure 磁盘加密概述

Azure 磁盘加密有助于保护你的数据以满足组织的安全性和符合性承诺。 它使用[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows 功能和[Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) Linux 提供卷加密的 OS 和数据磁盘的 Azure 虚拟机 (Vm) 的功能。 它还与集成[Azure 密钥保管库](https://azure.microsoft.com/documentation/services/key-vault/)来帮助你控制和管理磁盘加密密钥和机密，并可确保 VM 磁盘上的所有数据在 Azure 存储中静态都加密。 Azure 磁盘加密的 Windows 和 Linux Vm 通常是在所有 Azure 公共区域和标准 Vm 和使用 Azure 高级存储 Vm 的 Azure 政府版区域中的可用性。 

如果使用 Azure 安全中心，当 VM 未加密时，你会收到警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。


## <a name="encryption-scenarios"></a>加密方案

使用 Azure 磁盘加密，可以通过来解决组织的安全性和合规性要求保护 Azure Vm 使用的行业标准加密技术。 此外可以配置要在客户控制的密钥和策略 (BYOK)，启动 Vm 并审核密钥保管库中的这些密钥的使用情况。

Azure 磁盘加密支持以下客户方案：

* 启用和禁用通过受支持的 Azure 库映像创建的新 Vm 上的加密。
* 启用和禁用加密在 Azure 中运行的现有 Vm 上。
* 启用和禁用通过预加密的 VHD 和加密密钥创建的新 Windows Vm 上的加密。
* 启用和禁用加密 Windows 虚拟机规模集。
* 对于 Linux 虚拟机规模集的启用和禁用对数据进行加密的驱动器。
* 启用和禁用加密的托管磁盘 Vm。
* 正在更新现有加密的高级和非高级存储 VM 的加密设置。
* 备份和还原加密的 Vm。
* 将你自己的加密 (BYOE)，并创建你自己密钥 (BYOK) 的方案中的客户使用他们自己的加密密钥，并将其存储在 Azure 密钥保管库。

它还支持以下方案的 Vm 时已在 Microsoft Azure 中启用这些：

* 与 Azure Key Vault 集成。
* [标准层 Vm](https://azure.microsoft.com/pricing/details/virtual-machines/)。 这些层中的 [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 必须满足最小 7 GB 内存要求。 
* 启用加密 Windows 和 Linux 虚拟机、 托管的磁盘和规模集通过受支持的 Azure 库映像的 Vm。
* 禁用对 OS 和数据加密驱动器对于 Windows Vm，规模集 Vm，并托管磁盘 Vm。
* 禁用加密数据驱动器上的 Linux Vm，规模集 Vm，并托管磁盘 Vm。
* 运行 Windows 客户端 OS 的 Vm 上启用加密。
* 包含装入路径的卷上启用加密。
* 配置了使用 mdadm 进行条带化 (RAID) 的磁盘的 Linux Vm 上启用加密。
* 使用 LVM 对数据磁盘的 Linux Vm 上启用加密。
* 上的 Linux VM OS 和数据磁盘启用加密。

   > [!NOTE]
   > 不支持对某些 Linux 分发版进行 OS 驱动器加密。 有关详细信息，请参阅 [Azure 磁盘加密常见问题解答](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)一文。
   
* 使用 Windows 存储空间开始 Windows Server 2016 中配置的 Vm 上启用加密。
* 备份和还原加密密钥加密密钥 (KEK) 和非 KEK 方案的 Vm。

Azure 磁盘加密并不适用于以下方案、 功能和技术：

* 对基本层 VM 或通过经典 VM 创建方法创建的 Vm 进行加密。
* 加密 OS 驱动器时，请禁用加密 OS 驱动器或 Linux VM 的数据驱动器上。
* 加密 OS 驱动器为 Linux 虚拟机规模设置。
* 加密 Windows Vm 使用基于软件的 RAID 系统配置。
* 加密 Linux Vm 上的自定义映像。
* 与本地密钥管理系统集成。
* Azure 文件（共享文件系统）。
* 网络文件系统 (NFS)。
* 动态卷。

## <a name="encryption-features"></a>加密功能

启用并部署 Azure Vm 的 Azure 磁盘加密，可以配置启用以下功能：

* 加密 OS 卷以轻松保护存储中的引导卷。
* 若要保护你的存储中的静态数据卷的数据卷进行加密。
* 适用于 Windows Vm 的 OS 和数据驱动器上禁用加密。
* 驱动器上禁用加密数据适用于 Linux Vm （仅当 OS 驱动器未加密）。
* 保护加密密钥和机密在 Azure 密钥保管库订阅中。
* 报告已加密 VM 的加密状态。
* 从 VM 中删除的磁盘加密配置设置。
* 备份和还原加密的 Vm 使用 Azure 备份服务。

适用于 Vm 的 Windows 和 Linux 的 azure 磁盘加密包括：

* [适用于 Windows 的磁盘加密扩展](../virtual-machines/extensions/azure-disk-enc-windows.md)。
* [适用于 Linux 的磁盘加密扩展](../virtual-machines/extensions/azure-disk-enc-linux.md)。
* [PowerShell 磁盘加密 cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)。
* [Azure CLI 磁盘加密 cmdlet](/cli/azure/vm/encryption?view=azure-cli-latest)。
* [Azure 资源管理器磁盘加密模板](azure-security-disk-encryption-appendix.md#resource-manager-templates)。

在运行 Windows 或 Linux OS 的 Vm 上支持 azure 磁盘加密。 有关支持的操作系统的详细信息，请参阅[方面的常见问题](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)。

> [!NOTE]
> 可以免费使用 Azure 磁盘加密来加密 VM 磁盘。 标准 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)适用于用来存储加密密钥的 Key Vault。 

## <a name="encryption-workflow"></a>加密工作流

若要为 Windows 和 Linux VM 启用磁盘加密，请执行以下步骤：

1. 选择通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 Azure CLI 启用磁盘加密，并指定加密配置。

   * 对于客户加密的 VHD 方案，将加密的 VHD 上传到存储帐户，并将加密密钥材料上传到 Key Vault。 然后，提供加密配置以便在新的 VM 上启用加密。
   * 有关受支持的库，从映像创建的新 Vm 和已在 Azure 中运行的现有 Vm，提供加密配置以便在 VM 上启用加密。

1. 授予对 Azure 平台从密钥保管库以在 VM 上启用加密读取加密密钥材料 （Windows 系统的 BitLocker 加密密钥） 和适用于 Linux 的通行短语的访问权限。

1. Azure 使用加密和 Key Vault 配置更新 VM 服务模型，并设置加密的 VM。

   ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>解密工作流
若要禁用 Vm 的磁盘加密，请完成以下高级步骤：

1. 若要禁用加密 （解密） 上选择在 Azure 中正在运行的 VM，并指定解密配置。 可以通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 Azure CLI 禁用加密。

   此步骤将禁用操作系统中的数据量和 / 或正在运行的 Windows VM 上的加密。 如前面部分所述，不支持对 Linux 禁用 OS 磁盘加密。 只要 OS 磁盘未加密，仅允许对 Linux VM 上的数据驱动器执行解密步骤。

1. Azure 更新 VM 服务模型和 VM 标记为解密。 VM 的内容不再静态加密。

   > [!NOTE]
   > 禁用加密操作不会删除 Key Vault 和加密密钥材料（Windows 系统的 BitLocker 加密密钥，或 Linux 的通行短语）。
   >
   > 不支持禁用 Linux 的 OS 磁盘加密。 仅允许对 Linux VM 上的数据驱动器执行解密步骤。
   >
   > 如果 OS 驱动器已加密，则不支持对 Linux 禁用数据磁盘加密。


## <a name="encryption-workflow-previous-release"></a>加密工作流（以前的版本）

新版本的 Azure 磁盘加密无需提供 Azure Active Directory (Azure AD) 应用程序参数即可启用 VM 磁盘加密。 如果使用新版本，则不再需要在启用加密步骤期间提供 Azure AD 凭据。 使用新版本时，所有不带 Azure AD 应用程序参数的新 VM 必须经过加密。 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 Azure AD 语法进行维护。 若要为 Windows 和 Linux VM 启用磁盘加密（以前的版本），请执行以下步骤：

1. 从[加密方案](#encryption-scenarios)所列的方案中选择一种加密方案。

1. 选择通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 Azure CLI 启用磁盘加密，并指定加密配置。

   * 对于客户加密的 VHD 方案，将加密的 VHD 上传到存储帐户，并将加密密钥材料上传到 Key Vault。 然后，提供加密配置以便在新的 VM 上启用加密。
   * 有关从 Marketplace 创建的新 Vm 和已在 Azure 中运行的现有 Vm，提供加密配置以便在 VM 上启用加密。

1. 授予对 Azure 平台从密钥保管库以在 VM 上启用加密读取加密密钥材料 （Windows 系统的 BitLocker 加密密钥） 和适用于 Linux 的通行短语的访问权限。

1. 提供 Azure AD 应用程序标识，将加密密钥材料写入 Key Vault。 此步骤启用在步骤 2 中所述方案在 VM 上的加密。

1. Azure 使用加密和 Key Vault 配置更新 VM 服务模型，并设置加密的 VM。


## <a name="terminology"></a>术语
下表定义了一些使用 Azure 磁盘加密文档中的常用术语：

| 术语 | 定义 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 帐户用于身份验证、存储以及从 Key Vault 检索机密。 |
| Azure 密钥保管库 | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)是一种行业认可的 Windows 卷加密技术，用于 Windows Vm 上启用磁盘加密。 |
| BEK | BitLocker 加密密钥 (BEK) 用于加密 OS 引导卷和数据卷。 BEK 在 Key Vault 中以机密形式进行保护。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是基于 Linux 的透明磁盘加密子系统，可用于 Linux Vm 上启用磁盘加密。 |
| 密钥加密密钥 (KEK) | 非对称密钥 (RSA 2048)，可用于保护或包装机密。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。

