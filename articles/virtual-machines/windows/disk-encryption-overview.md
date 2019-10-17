---
title: 为 Windows Vm 启用 Azure 磁盘加密
description: 本文提供了有关为 Windows Vm 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435719"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>适用于 Windows Vm 的 Azure 磁盘加密 

Azure 磁盘加密有助于保护和保护数据，以满足组织的安全性和符合性承诺。 它使用 Windows 的[Bitlocker](https://en.wikipedia.org/wiki/BitLocker)功能为 Azure 虚拟机（vm）的 OS 和数据磁盘提供卷加密，并与[Azure Key Vault](../../key-vault/index.yml)集成，以帮助你控制和管理磁盘加密密钥和机密。 

如果你使用的是[Azure 安全中心](../../security-center/index.yml)，则如果你有未加密的 vm，系统会发出警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果以前通过 Azure AD 使用 Azure 磁盘加密来加密 VM，则必须继续使用此选项对 VM 进行加密。 有关详细信息，请参阅[Azure 磁盘加密与 Azure AD （以前的版本）](disk-encryption-overview-aad.md) 。 
> - 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。 必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源。

只需几分钟即可了解适用于 Windows 的 Azure 磁盘加密的基本知识，只需使用 "[创建和加密 WINDOWS vm" Azure CLI 快速入门](disk-encryption-cli-quickstart.md)或[使用 Azure Powershell 快速入门创建和加密 windows vm](disk-encryption-powershell-quickstart.md)即可。

## <a name="supported-vms-and-operating-systems"></a>支持的 Vm 和操作系统

### <a name="supported-vm-sizes"></a>支持的 VM 大小

Windows Vm 在[大小范围](sizes-general.md)内可用。 Azure 磁盘加密不适用于[基本、A 系列 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)或内存小于 2 GB 的虚拟机。

使用高级存储的 Vm 也可以使用 Azure 磁盘加密。

### <a name="supported-operating-systems"></a>支持的操作系统

- Windows 客户端： Windows 8 及更高版本。
- Windows Server： Windows Server 2008 R2 及更高版本。  
 
> [!NOTE]
> Windows Server 2008 R2 需要安装 .NET Framework 4.5 才能进行加密;将其从 Windows 更新安装，其中包含适用于基于 Windows Server 2008 R2 x64 的系统的可选更新 Microsoft .NET Framework 4.5.2 （[KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)）。  
>  
> Windows Server 2012 R2 Core 和 Windows Server 2016 Core 要求在虚拟机上安装 bdehdcfg 组件。


## <a name="networking-requirements"></a>网络要求
若要启用 Azure 磁盘加密，Vm 必须满足以下网络终结点配置要求：
  - 若要获取令牌以连接到密钥保管库，Windows VM 必须能够连接到 Azure Active Directory 终结点，\[login @ no__t。
  - 若要将加密密钥写入密钥保管库，Windows VM 必须能够连接到密钥保管库终结点。
  - Windows VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。    


## <a name="group-policy-requirements"></a>组策略要求

Azure 磁盘加密使用适用于 Windows Vm 的 BitLocker 外部密钥保护程序。 对于已加入域的 VM，请不要推送会强制执行 TPM 保护程序的任何组策略。 有关“在没有兼容 TPM 的情况下允许 BitLocker”的组策略信息，请参阅 [BitLocker 组策略参考](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

与自定义组策略在加入域的虚拟机上的 BitLocker 策略必须包括以下设置：[配置 BitLocker 恢复信息的用户存储-> 允许256位恢复密钥](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 如果 BitLocker 的自定义组策略设置不兼容，Azure 磁盘加密将会失败。 在没有正确策略设置的计算机上，应用新策略，强制更新新策略 (gpupdate.exe /force)，然后可能需要重启。

如果域级别组策略阻止 BitLocker 使用的 AES-CBC 算法，Azure 磁盘加密将会失败。

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求  

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 Key vault 和 Vm 必须位于同一个 Azure 区域和订阅中。

有关详细信息，请参阅[创建和配置 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。

## <a name="terminology"></a>术语
下表定义了 Azure 磁盘加密文档中使用的一些常用术语：

| 术语 | 定义 |
| --- | --- |
| Azure 密钥保管库 | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)文档和[为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)是一种行业认可的 windows 卷加密技术，用于在 Windows vm 上启用磁盘加密。 |
| 密钥加密密钥（KEK） | 可用于保护或包装机密的非对称密钥（RSA 2048）。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)文档和[为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>后续步骤

- [快速入门-使用 Azure CLI 创建和加密 Windows VM](disk-encryption-cli-quickstart.md)
- [快速入门-使用 Azure Powershell 创建和加密 Windows VM](disk-encryption-powershell-quickstart.md)
- [Windows Vm 上的 Azure 磁盘加密方案](disk-encryption-windows.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)


