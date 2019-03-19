---
title: PowerShell - 轮换 TDE 保护器 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何轮换 Azure SQL Server 的透明数据加密 (TDE) 保护器。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895645"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 轮换透明数据加密 (TDE) 保护器

本文介绍使用 Azure Key Vault 中的 TDE 保护器针对 Azure SQL Server 进行的密钥轮换。 轮换 Azure SQL Server 的 TDE 保护器意味着切换到新的非对称密钥，后者可以对服务器上的数据库进行保护。 密钥轮换是一种联机操作，应该只需数秒即可完成，因为只需在解密数据库的数据加密密钥后重新将其加密即可，不需对整个数据库进行操作。

本指南介绍在服务器上轮换 TDE 保护器的两个选项。

> [!NOTE]
> 在密钥轮换之前，必须恢复暂停的 SQL 数据仓库。
>

> [!IMPORTANT]
> 在滚动更新之后，**请勿删除**旧版密钥。  滚动更新密钥时，某些数据仍使用以前的密钥进行加密，例如旧版数据库备份。 
>

## <a name="prerequisites"></a>必备组件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库，但未来的所有开发都不适用于 Az.Sql 模块。 有关这些 cmdlet，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 命令在 Az 模块和 AzureRm 模块中的参数是大体上相同的。

- 本操作方法指南假设已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或数据仓库的 TDE 保护器。 请参阅[提供 BYOK 支持的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。
- 您必须安装并运行 Azure PowerShell。
- [推荐但仅为可选] 先在硬件安全模块 (HSM) 或本地密钥存储中创建 TDE 保护器的密钥材料，然后将密钥材料导入到 Azure Key Vault。 按[硬件安全模块 (HSM) 和 Key Vault 的使用说明](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)操作，以便了解详细信息。

## <a name="manual-key-rotation"></a>手动密钥轮换

使用手动密钥轮换[添加 AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey)，[添加 AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)，和[集 AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 来添加完全新密钥，可能是新的密钥名称或甚至另一个密钥保管库下。 使用此方法时支持将同一密钥添加到不同的密钥保管库来支持高可用性和异地灾难恢复方案。

>[!NOTE]
>Key Vault 名称和密钥名称的总长度不能超过 94 个字符。

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

## <a name="option-2-manual-rotation"></a>选项 2：手动轮换

此选项使用[添加 AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)，[添加 AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)，并[集 AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 来添加完全新密钥，可能是新的密钥名称或甚至另一个密钥保管库下。 

>[!NOTE]
>Key Vault 名称和密钥名称的总长度不能超过 94 个字符。
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>其他有用的 PowerShell cmdlet

- 若要切换的 TDE 保护程序从 Microsoft 托管到 BYOK 模式，请使用[集 AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- 若要切换的 TDE 保护程序从 BYOK 模式为 Microsoft 托管，请使用[集 AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>后续步骤

- 了解如何在出现安全风险的情况下，删除可能已泄漏的 TDE 保护器：[删除可能已泄漏的密钥](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- 开始使用 TDE 的 Azure Key Vault 集成和自带密钥支持：[通过 PowerShell 并使用 Key Vault 中自己的密钥来启用 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
