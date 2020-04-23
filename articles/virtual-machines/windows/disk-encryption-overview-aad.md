---
title: 使用 Azure AD 进行 Azure 磁盘加密（以前版本）
description: 本文提供了对 IaaS VM 使用 Microsoft Azure 磁盘加密所要满足的先决条件。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 025d02ccdf38e72682cf67cc07a8b2edd549e599
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081568"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>使用 Azure AD 进行 Azure 磁盘加密（以前版本）

**新版本的 Azure 磁盘加密消除了提供 Azure AD 应用程序参数以启用 VM 磁盘加密的要求。使用新版本时，不再需要在启用加密步骤期间提供 Azure AD 凭据。使用新版本，必须在没有 Azure AD 应用程序参数的情况下对所有新 VM 进行加密。要查看使用新版本启用 VM 磁盘加密的说明，请参阅 Windows [VM 的 Azure 磁盘加密](disk-encryption-overview.md)。仍支持已使用 Azure AD 应用程序参数加密的 VM，应继续使用 AAD 语法进行维护。**

本文通过使用 Azure AD 进行 Azure 磁盘加密的其他要求和先决条件（以前版本）补充说明了[适用于 Windows VM 的 Azure 磁盘加密](disk-encryption-overview.md)。 [支持的 VM 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)部分保持不变。

## <a name="networking-and-group-policy"></a>网络和组策略

**若要启用使用旧 AAD 参数语法的 Azure 磁盘加密功能，IaaS VM 必须符合以下网络终结点配置要求：** 
  - IaaS VM 必须能够连接到 Azure Active Directory 终结点 \[login.microsoftonline.com\]，以获取用于连接 Key Vault 的令牌。
  - IaaS VM 必须能够连接到 Key Vault 终结点，以将加密密钥写入 Key Vault。
  - IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - 要加密的 VM 必须配置为使用 TLS 1.2 作为默认协议。 如果 TLS 1.0 已显式禁用，并且 .NET 版本未更新到 4.6 或更高版本，则以下注册表更改将使 ADE 能够选择较新的 TLS 版本：

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**组策略：**
 - Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 对于已加入域的 VM，请不要推送会强制执行 TPM 保护程序的任何组策略。 有关“在没有兼容 TPM 的情况下允许 BitLocker”的组策略信息，请参阅 [BitLocker 组策略参考](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  具有自定义组策略的域加入虚拟机上的 BitLocker 策略必须包括以下设置：[配置用户存储的 BitLocker 恢复信息 ->允许 256 位恢复密钥](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 如果 BitLocker 的自定义组策略设置不兼容，Azure 磁盘加密将会失败。 在没有正确策略设置的计算机上，应用新策略，强制更新新策略 (gpupdate.exe /force)，然后可能需要重启。  

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求  

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 密钥保管库和 VM 必须位于同一 Azure 区域和订阅中。

有关详细信息，请参阅[使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](disk-encryption-key-vault-aad.md)
- [在 Windows VM 上使用 Azure AD 启用 Azure 磁盘加密（以前版本）](disk-encryption-windows-aad.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
