---
title: Azure Disk Encryption with Azure AD app prerequisites (previous release)
description: 本文提供了对 IaaS VM 使用 Microsoft Azure 磁盘加密所要满足的先决条件。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e1b9df750886af050163a85e2c6a3539bd63c733
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457199"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption with Azure AD (previous release)

The new release of Azure Disk Encryption eliminates the requirement for providing an Azure Active Directory (Azure AD) application parameter to enable VM disk encryption. 使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。 All new VMs must be encrypted without the Azure AD application parameters by using the new release. For instructions on how to enable VM disk encryption by using the new release, see [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md). 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。

This article provides supplements to [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md) with additional requirements and prerequisites for Azure Disk Encryption with Azure AD (previous release).

The information in these sections remains the same:

- [Supported VMs and operating systems](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Additional VM requirements](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Networking and Group Policy

To enable the Azure Disk Encryption feature by using the older AAD parameter syntax, the infrastructure as a service (IaaS) VMs must meet the following network endpoint configuration requirements: 
  - To get a token to connect to your key vault, the IaaS VM must be able to connect to an Azure AD endpoint, \[login.microsoftonline.com\].
  - IaaS VM 必须能够连接到 Key Vault 终结点，以将加密密钥写入 Key Vault。
  - IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  If your security policy limits access from Azure VMs to the internet, you can resolve the preceding URI and configure a specific rule to allow outbound connectivity to the IPs. 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - On Windows, if TLS 1.0 is explicitly disabled and the .NET version isn't updated to 4.6 or higher, the following registry change enables Azure Disk Encryption to select the more recent TLS version:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>组策略
 - Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 For domain-joined VMs, don't push any Group Policies that enforce TPM protectors. For information about the Group Policy for the option **Allow BitLocker without a compatible TPM**, see [BitLocker Group Policy reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- BitLocker policy on domain-joined virtual machines with a custom Group Policy must include the following setting: [Configure user storage of BitLocker recovery information -> Allow 256-bit recovery key](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption fails when custom Group Policy settings for BitLocker are incompatible. On machines that don't have the correct policy setting, apply the new policy, force the new policy to update (gpupdate.exe /force), and then restart if it's required. 

## <a name="encryption-key-storage-requirements"></a>Encryption key storage requirements 

Azure Disk Encryption requires Azure Key Vault to control and manage disk encryption keys and secrets. Your key vault and VMs must reside in the same Azure region and subscription.

For more information, see [Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>后续步骤

- [Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](disk-encryption-key-vault-aad.md)
- [Enable Azure Disk Encryption with Azure AD on Linux VMs (previous release)](disk-encryption-linux-aad.md)
- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)