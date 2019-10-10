---
title: 为 Azure 磁盘加密创建和配置密钥保管库
description: 本文介绍如何创建和配置用于 Azure 磁盘加密的密钥保管库
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245235"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>为 Azure 磁盘加密创建和配置密钥保管库

Azure 磁盘加密使用 Azure Key Vault 来控制和管理磁盘加密密钥和机密。  有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../../key-vault/key-vault-get-started.md)和[保护 Key Vault](../../key-vault/key-vault-secure-your-key-vault.md)。 

> [!WARNING]
> - 如果以前通过 Azure AD 使用 Azure 磁盘加密来加密 VM，则必须继续使用此选项对 VM 进行加密。 有关详细信息，请参阅[使用 Azure AD （以前的版本）创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault-aad.md)。

创建和配置用于 Azure 磁盘加密的 key vault 涉及三个步骤：

1. 创建资源组（如果需要）。
2. 正在创建密钥保管库。 
3. 正在设置密钥保管库高级访问策略。

以下快速入门中阐释了这些步骤：

- [使用 Azure CLI 创建和加密 Windows VM](disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 创建和加密 Windows VM](disk-encryption-cli-quickstart.md)

你还可以根据需要生成或导入密钥加密密钥（KEK）。

> [!Note]
> 本文中的步骤是在[Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)和[azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自动完成的。

## <a name="install-tools-and-connect-to-azure"></a>安装工具并连接到 Azure

本文中的步骤可以通过[Azure CLI](/cli/azure/)、 [Azure PowerShell Az module](/powershell/azure/overview)或[Azure 门户](https://portal.azure.com)来完成。

当门户可通过浏览器访问时，Azure CLI 和 Azure PowerShell 需要本地安装;请参阅 [Azure Disk Encryption for Windows：安装工具 @ no__t-0 获取详细信息。

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 Azure CLI 或 Azure PowerShell 之前，必须先连接到 Azure 订阅。 为此，请[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)，[使用 Azure Powershell 登录](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出现提示时向 Azure 门户提供凭据。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- 了解[Windows vm 上的 Azure 磁盘加密方案](disk-encryption-windows.md)
- 了解如何[排查 Azure 磁盘加密问题](disk-encryption-troubleshooting.md)
- 阅读[Azure 磁盘加密示例脚本](disk-encryption-sample-scripts.md)
