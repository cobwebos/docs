---
title: 具有 Azure AD 应用必备组件的 Azure 磁盘加密（以前的版本）
description: 本文提供了对 IaaS VM 使用 Microsoft Azure 磁盘加密所要满足的先决条件。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970582"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure 磁盘加密与 Azure AD （以前的版本）

新版本的 Azure 磁盘加密消除了提供 Azure Active Directory （Azure AD）应用程序参数以启用 VM 磁盘加密的要求。 使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。 在不使用新版本的情况下，必须使用 Azure AD 的应用程序参数对所有新的 Vm 进行加密。 有关如何使用新版本启用 VM 磁盘加密的说明，请参阅适用于[Linux vm 的 Azure 磁盘加密](disk-encryption-overview.md)。 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。

本文提供了对[Linux vm 的 Azure 磁盘加密](disk-encryption-overview.md)的补充，其中包含针对 Azure AD （以前版本）的 Azure 磁盘加密的其他要求和先决条件。

这些部分中的信息将保持不变：

- [支持的 Vm 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>网络和组策略

若要通过使用较旧的 AAD 参数语法来启用 Azure 磁盘加密功能，基础结构即服务（IaaS） Vm 必须满足以下网络终结点配置要求： 
  - 若要获取令牌以连接到密钥保管库，IaaS VM 必须能够连接到 Azure AD 终结点，\[login.microsoftonline.com\]。
  - IaaS VM 必须能够连接到 Key Vault 终结点，以将加密密钥写入 Key Vault。
  - IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure Vm 到 internet 的访问，可以解析上述 URI，并配置特定的规则以允许与 Ip 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - 在 Windows 上，如果显式禁用了 TLS 1.0，并且 .NET 版本未更新为4.6 或更高版本，则通过以下注册表更改，Azure 磁盘加密可选择最新的 TLS 版本：
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>组策略
 - Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 对于已加入域的 Vm，不要推送任何强制 TPM 保护程序的组策略。 有关选项的组策略**允许无兼容 TPM 的 bitlocker**的信息，请参阅[bitlocker 组策略参考](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

- 使用自定义组策略的已加入域的虚拟机上的 BitLocker 策略必须包括以下设置：[配置 BitLocker 恢复信息的用户存储-> 允许256位恢复密钥](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 当 BitLocker 的自定义组策略设置不兼容时，Azure 磁盘加密会失败。 在没有正确的策略设置的计算机上，应用新策略，强制更新新策略（gpupdate/force），然后在需要时重新启动。 

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求 

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 Key vault 和 Vm 必须位于同一个 Azure 区域和订阅中。

有关详细信息，请参阅[使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前的版本）](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 为 Azure 磁盘加密创建和配置密钥保管库（以前的版本）](disk-encryption-key-vault-aad.md)
- [使用 Linux Vm 上的 Azure AD 启用 Azure 磁盘加密（以前的版本）](disk-encryption-linux-aad.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)