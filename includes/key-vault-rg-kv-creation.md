---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512784"
---
本快速入门使用预先创建的 Azure Key Vault。 可以遵循 [Azure CLI 快速入门](/azure/key-vault/general/quick-create-cli)、[Azure PowerShell 快速入门](/azure/key-vault/general/quick-create-powershell)或 [Azure 门户快速入门](/azure/key-vault/general/quick-create-portal)中的步骤创建 Key Vault。 

或者，只需运行以下 Azure CLI 或 Azure PowerShell 命令。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```