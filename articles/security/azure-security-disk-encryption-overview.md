---
title: 什么是 Azure 磁盘加密？
description: 本文提供 Azure 磁盘加密概述
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/17/2019
ms.custom: seodec18
ms.openlocfilehash: a67f19f0823827dad74e7aba15a92d696fbf580b
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304270"
---
# <a name="azure-disk-encryption-overview"></a>Azure 磁盘加密概述

Azure 磁盘加密有助于保护和保护数据, 以满足组织的安全性和符合性承诺。 它使用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能为 Azure 虚拟机 (vm) 的 OS 和数据磁盘提供卷加密。 它还与[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)集成, 可帮助你控制和管理磁盘加密密钥和机密, 并可确保 VM 磁盘上的所有数据在 Azure 存储中静态加密。 适用于 Windows 和 Linux Vm 的 azure 磁盘加密已在所有 Azure 公共区域和 Azure 政府区域中公开上市, 适用于 Azure 高级存储的标准 Vm 和 Vm。 

如果使用 Azure 安全中心，当 VM 未加密时，你会收到警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。


## <a name="encryption-scenarios"></a>加密方案

使用 Azure 磁盘加密, 可以使用行业标准的加密技术来保护静态 Azure Vm, 从而满足组织的安全性和符合性要求。 你还可以将 Vm 配置为在客户控制的密钥和策略 (BYOK) 下启动, 并审核密钥保管库中的这些密钥的使用情况。

Azure 磁盘加密支持以下客户方案:

* 在从支持的 Azure 库映像中创建的新 Vm 上启用和禁用加密。
* 在 Azure 中运行的现有 Vm 上启用和禁用加密。
* 在通过预加密 VHD 和加密密钥创建的新 Windows Vm 上启用和禁用加密。
* 启用和禁用对 Windows 虚拟机规模集进行加密。
* 为 Linux 虚拟机规模集启用和禁用数据驱动器加密。
* 启用和禁用托管磁盘 Vm 的加密。
* 更新现有加密的高级和非高级存储 VM 的加密设置。
* 备份和还原加密的 Vm。
* 自带加密 (BYOE) 并自带密钥 (BYOK) 方案, 其中客户使用自己的加密密钥, 并将其存储在 Azure 密钥保管库中。

它还支持在 Microsoft Azure 中启用 Vm 时的以下方案:

* 与 Azure Key Vault 集成。
* 满足[最低内存要求](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes)的[标准层虚拟机](https://azure.microsoft.com/pricing/details/virtual-machines/)。 
* 在受支持的 Azure 库映像中启用 Windows 和 Linux Vm、托管磁盘和规模集 Vm 上的加密。
* 禁用 Windows Vm、规模集 Vm 和托管磁盘 Vm 的 OS 和数据驱动器上的加密。
* 禁用适用于 Linux Vm、规模集 Vm 和托管磁盘 Vm 的数据驱动器加密。
* 在运行 Windows 客户端 OS 的 Vm 上启用加密。
* 在包含装入路径的卷上启用加密。
* 在使用 mdadm 配置了磁盘条带化 (RAID) 的 Linux Vm 上启用加密。
* 在使用 LVM 用于数据磁盘的 Linux Vm 上启用加密。
* 启用对 Linux VM 操作系统和数据磁盘的加密。

   > [!NOTE]
   > 不支持对某些 Linux 分发版进行 OS 驱动器加密。 有关详细信息, 请参阅[Azure 磁盘加密支持的操作系统:Linux](azure-security-disk-encryption-prerequisites.md#linux)。
   
* 在从 Windows Server 2016 开始的 Windows 存储空间配置的 Vm 上启用加密。 尚不支持存储空间直通 (S2D)。
* 对于密钥加密密钥 (KEK) 和非 KEK 方案, 备份和还原加密的 Vm。

Azure 磁盘加密不适用于以下方案、功能和技术:

* 对基本层 VM 或通过经典 VM 创建方法创建的 Vm 进行加密。
* 在对操作系统驱动器进行加密时, 在 Linux VM 的 OS 驱动器或数据驱动器上禁用加密。
* 为 Linux 虚拟机规模集加密 OS 驱动器。
* 加密配置了基于软件的 RAID 系统的 Windows Vm。
* 加密 Linux Vm 上的自定义映像。
* 与本地密钥管理系统集成。
* Azure 文件（共享文件系统）。
* 网络文件系统 (NFS)。
* 动态卷。

## <a name="encryption-features"></a>加密功能

为 Azure Vm 启用并部署 Azure 磁盘加密后, 可以配置要启用的以下功能:

* 加密 OS 卷以保护存储中的静态启动卷。
* 加密数据卷以保护存储中静态的数据卷。
* 在 Windows Vm 的 OS 和数据驱动器上禁用加密。
* 在 Linux Vm 的数据驱动器上禁用加密 (仅当 OS 驱动器未加密时)。
* 保护 Azure Key Vault 订阅中的加密密钥和机密。
* 报告加密 VM 的加密状态。
* 正在从 VM 中删除磁盘加密配置设置。
* 使用 Azure 备份服务备份和还原已加密的 Vm。

适用于 Windows 和 Linux 的 Vm 的 Azure 磁盘加密包括:

* [适用于 Windows 的磁盘加密扩展](../virtual-machines/extensions/azure-disk-enc-windows.md)。
* [适用于 Linux 的磁盘加密扩展](../virtual-machines/extensions/azure-disk-enc-linux.md)。
* [PowerShell 磁盘加密 cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)。
* [Azure CLI 磁盘加密 cmdlet](/cli/azure/vm/encryption?view=azure-cli-latest)。
* [Azure 资源管理器磁盘加密模板](azure-security-disk-encryption-appendix.md#resource-manager-templates)。

> [!NOTE]
> 可以免费使用 Azure 磁盘加密来加密 VM 磁盘。 标准 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)适用于用来存储加密密钥的 Key Vault。 

## <a name="encryption-workflow"></a>加密工作流

若要为 Windows 和 Linux VM 启用磁盘加密，请执行以下步骤：

1. 选择通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 Azure CLI 启用磁盘加密，并指定加密配置。

   * 对于客户加密的 VHD 方案，将加密的 VHD 上传到存储帐户，并将加密密钥材料上传到 Key Vault。 然后, 提供加密配置, 在新的 VM 上启用加密。
   * 对于从支持的库映像创建的新 Vm 和已在 Azure 中运行的现有 Vm, 提供加密配置以便在 VM 上启用加密。

1. 向 Azure 平台授予访问权限, 以便从密钥保管库中读取加密密钥材料 (适用于 Windows 系统的 BitLocker 加密密钥和 Linux 密码), 以在 VM 上启用加密。

1. Azure 使用加密和 Key Vault 配置更新 VM 服务模型，并设置加密的 VM。

   ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>解密工作流
若要禁用 Vm 的磁盘加密, 请完成以下高级步骤:

1. 选择在 Azure 中正在运行的 VM 上禁用加密 (解密), 并指定解密配置。 可以通过 Azure 磁盘加密资源管理器模板、PowerShell cmdlet 或 Azure CLI 禁用加密。

   此步骤将禁用正在运行的 Windows VM 上的操作系统或数据卷的加密。 如前面部分所述，不支持对 Linux 禁用 OS 磁盘加密。 只要 OS 磁盘未加密，仅允许对 Linux VM 上的数据驱动器执行解密步骤。

1. Azure 更新 VM 服务模型, VM 被标记为已解密。 VM 的内容不再静态加密。

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

   * 对于客户加密的 VHD 方案，将加密的 VHD 上传到存储帐户，并将加密密钥材料上传到 Key Vault。 然后, 提供加密配置, 在新的 VM 上启用加密。
   * 对于通过 Marketplace 创建的新 Vm 和已在 Azure 中运行的现有 Vm, 提供加密配置, 在 VM 上启用加密。

1. 向 Azure 平台授予访问权限, 以便从密钥保管库中读取加密密钥材料 (适用于 Windows 系统的 BitLocker 加密密钥和 Linux 密码), 以在 VM 上启用加密。

1. 提供 Azure AD 应用程序标识，将加密密钥材料写入 Key Vault。 此步骤将在步骤2中所述的方案中为 VM 启用加密。

1. Azure 使用加密和 Key Vault 配置更新 VM 服务模型，并设置加密的 VM。


## <a name="terminology"></a>术语
下表定义了 Azure 磁盘加密文档中使用的一些常用术语:

| 术语 | 定义 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 帐户用于身份验证、存储以及从 Key Vault 检索机密。 |
| Azure 密钥保管库 | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)是一种行业认可的 windows 卷加密技术, 用于在 Windows vm 上启用磁盘加密。 |
| BEK | BitLocker 加密密钥 (BEK) 用于加密 OS 引导卷和数据卷。 BEK 在 Key Vault 中以机密形式进行保护。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| DM-Crypt |[DM dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是基于 linux 的透明磁盘加密子系统, 用于在 linux vm 上启用磁盘加密。 |
| 密钥加密密钥 (KEK) | 可用于保护或包装机密的非对称密钥 (RSA 2048)。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |

## <a name="next-steps"></a>后续步骤

若要开始, 请参阅[Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。

