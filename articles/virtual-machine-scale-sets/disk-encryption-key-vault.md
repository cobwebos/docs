---
title: 为 Azure 磁盘加密创建和配置密钥保管库
description: 本文介绍如何创建和配置用于 Azure 磁盘加密的密钥保管库
ms.service: virtual-machine-scale-sets
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0252fff84307577ab307e591948e16dc30c38f66
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749992"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>为 Azure 磁盘加密创建和配置密钥保管库

Azure 磁盘加密使用 Azure Key Vault 来控制和管理磁盘加密密钥和机密。  有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../key-vault/key-vault-get-started.md)和[保护 Key Vault](../key-vault/key-vault-secure-your-key-vault.md)。

创建和配置用于 Azure 磁盘加密的 key vault 涉及三个步骤：

1. 创建资源组（如果需要）。
2. 正在创建密钥保管库。 
3. 正在设置密钥保管库高级访问策略。

以下快速入门中阐释了这些步骤：

你还可以根据需要生成或导入密钥加密密钥（KEK）。

## <a name="install-tools-and-connect-to-azure"></a>安装工具并连接到 Azure

本文中的步骤可以通过[Azure CLI](/cli/azure/)、 [Azure PowerShell Az module](/powershell/azure/overview)或[Azure 门户](https://portal.azure.com)来完成。

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 Azure CLI 或 Azure PowerShell 之前，必须先连接到 Azure 订阅。 为此，请[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)，[使用 Azure Powershell 登录](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出现提示时向 Azure 门户提供凭据。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密概述](disk-encryption-overview.md)
- [使用 Azure CLI 加密虚拟机规模集](disk-encryption-cli.md)
- [使用 Azure PowerShell 加密虚拟机规模集](disk-encryption-powershell.md)
