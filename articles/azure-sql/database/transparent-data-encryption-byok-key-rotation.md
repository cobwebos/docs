---
title: 轮换 TDE 保护器（PowerShell 和 Azure CLI）
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: 了解如何使用 PowerShell 和 Azure CLI 为 Azure 中由 Azure SQL 数据库和 Azure Synapse Analytics 使用的服务器轮换透明数据加密 (TDE) 保护器。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 699434e89872b9525ea99883bc4fcbc86757ab8e
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619842"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>轮换透明数据加密 (TDE) 保护器
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


本文介绍使用 Azure Key Vault 中的 TDE 保护器对[服务器](logical-servers.md)进行的密钥轮换。 为服务器轮换逻辑 TDE 保护器意味着切换到新的非对称密钥，该密钥可以保护服务器上的数据库。 密钥轮换是一种联机操作，应该只需数秒即可完成，因为此操作只在解密数据库的数据加密密钥后重新将其加密，而不是对整个数据库进行操作。

本指南介绍在服务器上轮换 TDE 保护器的两个选项。

> [!NOTE]
> 需要在密钥轮换之前先恢复已暂停的 Azure Synapse Analytics SQL 池。

> [!IMPORTANT]
> 在滚动更新之后，请勿删除旧版密钥。 滚动更新密钥时，某些数据仍使用以前的密钥进行加密，例如旧版数据库备份。

## <a name="prerequisites"></a>先决条件

- 本操作方法指南假设已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或 Azure Synapse Analytics 的 TDE 保护器。 请参阅[提供 BYOK 支持的透明数据加密](transparent-data-encryption-byok-overview.md)。
- 必须安装并运行 Azure PowerShell。
- [推荐但仅为可选] 先在硬件安全模块 (HSM) 或本地密钥存储中创建 TDE 保护器的密钥材料，然后将密钥材料导入到 Azure Key Vault。 按[硬件安全模块 (HSM) 和 Key Vault 的使用说明](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)操作，以便了解详细信息。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 若要了解具体的 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器 (RM) 模块，但是所有未来的开发都是针对 Az.Sql 模块的。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

有关安装的信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="manual-key-rotation"></a>手动密钥轮换

手动密钥轮换使用以下命令来添加全新的密钥，该密钥可能使用新的密钥名称，甚至使用另一密钥保管库。 使用此方法时支持将同一密钥添加到不同的密钥保管库来支持高可用性和异地灾难恢复方案。

> [!NOTE]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey)、[Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 和 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet。

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)、[az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create) 和 [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) 命令。

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>切换 TDE 保护器模式

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- 若要将 TDE 保护器从 Microsoft 托管模式切换到 BYOK 模式，请使用 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- 若要将 TDE 保护器从 BYOK 模式切换到 Microsoft 托管模式，请使用 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

以下示例使用 [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)。

- 将 TDE 保护器从“Microsoft 托管”模式切换到“BYOK”模式；

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- 将 TDE 保护器从“BYOK”模式切换到“Microsoft 托管”模式；

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>后续步骤

- 了解如何在出现安全风险的情况下，删除可能已泄漏的 TDE 保护器：[删除可能已泄漏的密钥](transparent-data-encryption-byok-remove-tde-protector.md)。

- 开始使用 TDE 的 Azure Key Vault 集成和自带密钥支持：[通过 PowerShell 使用 Key Vault 中自己的密钥来启用 TDE](transparent-data-encryption-byok-configure.md)。
