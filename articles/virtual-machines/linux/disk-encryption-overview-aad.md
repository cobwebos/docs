---
title: Azure 磁盘加密与 Azure AD 应用先决条件（上一个版本）
description: 本文提供了对 IaaS VM 使用 Microsoft Azure 磁盘加密所要满足的先决条件。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970582"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>使用 Azure AD 进行 Azure 磁盘加密（以前版本）

Azure 磁盘加密的新版本消除了提供 Azure 活动目录 （Azure AD） 应用程序参数以启用 VM 磁盘加密的要求。 使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。 使用新版本，必须对所有新 VM 进行加密，而无需 Azure AD 应用程序参数。 有关如何使用新版本启用 VM 磁盘加密的说明，请参阅 Linux VM[的 Azure 磁盘加密](disk-encryption-overview.md)。 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。

本文为 Linux VM 提供了[Azure 磁盘加密](disk-encryption-overview.md)的补充，以及使用 Azure AD 进行 Azure 磁盘加密的其他要求和先决条件（上一版本）。

这些部分中的信息保持不变：

- [支持的 VM 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>网络和组策略

要使用较旧的 AAD 参数语法启用 Azure 磁盘加密功能，基础结构即服务 （IaaS） VM 必须满足以下网络终结点配置要求： 
  - 要获取令牌以连接到密钥保管库，IaaS VM 必须能够连接到 Azure AD 终结点，login.microsoftonline.com \[\]。
  - IaaS VM 必须能够连接到 Key Vault 终结点，以将加密密钥写入 Key Vault。
  - IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 访问 Internet，则可以解析前面的 URI 并配置特定规则以允许向外站连接到 IP。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - 在 Windows 上，如果 TLS 1.0 被显式禁用，并且 .NET 版本未更新到 4.6 或更高版本，则以下注册表更改使 Azure 磁盘加密能够选择较新的 TLS 版本：
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>组策略
 - Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 对于加入域的 VM，不要推送任何强制实施 TPM 保护器的组策略。 有关选项 **"允许未兼容 TPM 的 BitLocker"** 选项的组策略的信息，请参阅[BitLocker 组策略参考](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

- 具有自定义组策略的域加入虚拟机上的 BitLocker 策略必须包括以下设置：[配置 BitLocker 恢复信息的用户存储 ->允许 256 位恢复密钥](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 当 BitLocker 的自定义组策略设置不兼容时，Azure 磁盘加密将失败。 在没有正确策略设置的计算机上，应用新策略，强制更新新策略 （gpupdate.exe /force），然后根据需要重新启动。 

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求 

Azure 磁盘加密需要 Azure 密钥保管库来控制和管理磁盘加密密钥和机密。 密钥保管库和 VM 必须位于同一 Azure 区域和订阅中。

有关详细信息，请参阅使用[Azure AD 创建和配置 Azure 磁盘加密的密钥保管库（上一版本）。](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](disk-encryption-key-vault-aad.md)
- [在 Linux VM 上使用 Azure AD 启用 Azure 磁盘加密（以前版本）](disk-encryption-linux-aad.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)