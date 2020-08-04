---
title: 快速入门 - 使用 Azure PowerShell 创建 Azure Key Vault
description: 介绍如何使用 Azure PowerShell 创建 Azure Key Vault 的快速入门
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e24f1e546de1ce01896e271dbc9155c47f6c2bb6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100817"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>快速入门：使用 PowerShell 创建密钥保管库

Azure Key Vault 是一项云服务，它为[密钥](../keys/index.yml)、[机密](../secrets/index.yml)和[证书](../certificates/index.yml)提供了安全的存储。 有关 Key Vault 的详细信息，请参阅[关于 Azure Key Vault](overview.md)；有关密钥保管库中可存储的内容的详细信息，请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本快速入门中，你将使用 [Azure PowerShell](/powershell/azure/) 创建一个密钥保管库。 如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>创建 key vault

在上一步骤创建的资源组中创建 Key Vault。 需要提供某些信息：

- Key Vault 名称：由 3 到 24 个字符构成的字符串，只能包含数字 (0-9)、字母（a-z、A-Z）和连字符 (-)

  > [!Important]
  > 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

- 资源组名称：myResourceGroup。
- 位置：EastUS。

```azurepowershell-interactive
New-AzKeyVault -Name "&lt;your-unique-key-vault-name&gt; -ResourceGroupName "myResourceGroup" -Location "East US"
```

此 cmdlet 的输出显示新创建的密钥保管库的属性。 请记下下面列出的两个属性：

- **保管库名称**：为上面的 --name 参数提供的名称。
- **保管库 URI**：在本示例中为 https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用其他快速入门和教程，则可能需要保留这些资源。

如果不再需要资源组和所有相关资源，可以使用 Azure PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 Key Vault 并在其中存储了一个机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](overview.md)
- 请参阅 [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault) 参考
- 查看 [Azure Key Vault 最佳做法](best-practices.md)
