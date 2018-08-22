---
title: Azure SQL 数据库和数据仓库的透明数据加密 | Microsoft Docs
description: SQL 数据库和数据仓库的透明数据加密概述。 本文档介绍服务托管的透明数据加密和“创建自己的密钥”的优势与配置选项。
services: sql-database
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0ed05fd2d55f1c4c80bec9f64925be2eddddc067
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043282"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>SQL 数据库和数据仓库的透明数据加密

透明数据加密 (TDE) 有助于保护 Azure SQL 数据库和 Azure 数据仓库免受恶意活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。 默认情况下，为所有新部署的 Azure SQL 数据库启用了 TDE。 不能使用 TDE 来加密 SQL 数据库中的逻辑 **master** 数据库。  **master** 数据库包含对用户数据库执行 TDE 操作时所需的对象。

对于旧式数据库或 Azure SQL 数据仓库，需要手动启用 TDE。  

透明数据加密使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。 此数据库加密密钥受透明数据加密保护器的保护。 保护器是服务托管的证书（服务托管的透明数据加密）或存储在 Azure Key Vault 中的非对称密钥（“创建自己的密钥”）。 可在服务器级别设置透明数据加密保护器。 

数据库启动时，加密的数据库加密密钥将会解密，然后在 SQL Server 数据库引擎进程中用于解密和重新加密数据库文件。 透明数据加密在页面级别对数据执行实时 I/O 加密和解密。 将每个页面读入内存时会将其解密，在写入磁盘之前会将其加密。 有关透明数据加密的一般说明，请参阅[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。

在 Azure 虚拟机上运行的 SQL Server 也可以使用 Key Vault 中的非对称密钥。 配置步骤与使用 SQL 数据库中的非对称密钥时不同。 有关详细信息，请参阅[使用 Azure Key Vault 进行可扩展的密钥管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。

## <a name="service-managed-transparent-data-encryption"></a>服务托管的透明数据加密

在 Azure 中，透明数据加密的默认设置是通过内置的服务器证书保护数据库加密密钥。 内置服务器证书是每个服务器特有的。 如果某个数据库存在异地复制关系，则主数据库和异地辅助数据库将受主数据库的父服务器密钥的保护。 如果两个数据库连接到同一个服务器，则它们共用相同的内置证书。 Microsoft 每隔 90 天自动轮换这些证书至少一次。

Microsoft 还可按需无缝移动和管理密钥，以实现异地复制和还原。 

> [!IMPORTANT]
> 默认情况下，会使用服务托管的透明数据加密将所有新建的 SQL 数据库加密。 默认不会加密 2017 年 5 月以前的现有数据库，以及通过还原、异地复制和数据库复制创建的数据库。
>

## <a name="bring-your-own-key"></a>创建自己的密钥

使用“创建自己的密钥”支持，可以控制透明数据加密密钥，以及谁可以在哪个时间访问这些密钥。 Key Vault 是 Azure 基于云的外部密钥管理系统，也是透明数据加密将“创建自己的密钥”支持集成到的第一个密钥管理服务。 使用“创建自己的密钥”支持时，数据库加密密钥将受 Key Vault 中存储的非对称密钥的保护。 非对称密钥永远不会离开 Key Vault。 服务器获取 Key Vault 的权限后，服务器会将基本密钥操作请求发送到 Key Vault。 可在服务器级别设置非对称密钥，该服务器下的所有数据库将继承该密钥。

使用“创建自己的密钥”支持，可以控制密钥轮换和 Key Vault 权限等密钥管理任务。 还可以删除密钥，以及对所有加密密钥启用审核/报告。 Key Vault 提供集中密钥管理，并使用受严格监控的硬件安全模块。 Key Vault 可以促成密钥和数据管理的分离，以帮助满足法规遵从要求。 有关 Key Vault 的详细信息，请参阅 [Key Vault 文档页](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

有关 SQL 数据库和数据仓库的支持“创建自己的密钥”的透明数据加密的详细信息，请参阅[支持“创建自己的密钥”的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。

若要开始使用支持“创建自己的密钥”的透明数据加密，请参阅操作方法指南[通过 PowerShell 使用 Key Vault 中的自有密钥启用透明数据加密](transparent-data-encryption-byok-azure-sql-configure.md)。

## <a name="move-a-transparent-data-encryption-protected-database"></a>移动受透明数据加密保护的数据库

对于 Azure 中的操作，不需要解密数据库。 源数据库或主数据库上的透明数据加密设置将以透明方式继承到目标。 包括的操作涉及到：
- 异地还原。
- 自助时间点还原。
- 还原已删除的数据库。
- 活动异地复制。
- 创建数据库副本。

导出受透明数据加密保护的数据库时，不会加密数据库的导出内容。 此导出内容存储在未加密的 BACPAC 文件中。 请务必适当保护 BACPAC 文件，并在完成新数据库导入后启用透明数据加密。

例如，如果从本地 SQL Server 实例导出 BACPAC 文件，则新数据库的导入内容不会自动加密。 同样，如果将 BACPAC 文件导出到本地 SQL Server 实例，则新数据库也不会自动加密。

向/从 SQL 数据库导出时例外。 新数据库中已启用透明数据加密，但 BACPAC 文件本身不会加密。

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>在 Azure 门户中管理透明数据加密

若要通过 Azure 门户配置透明数据加密，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份进行连接。 

在数据库级别设置透明数据加密。 若要在数据库中启用透明数据加密，请转到 [Azure 门户](https://portal.azure.com)，并使用 Azure 管理员或参与者帐户登录。 在用户数据库下找到透明数据加密设置。 默认会使用服务托管的透明数据加密。 将为包含数据库的服务器自动生成透明数据加密证书。 

![服务托管的透明数据加密](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

在服务器级别设置透明数据加密主密钥（也称为透明数据加密保护器）。 若要使用支持“创建自己的密钥”的透明数据加密，并使用 Key Vault 中的密钥来保护数据库，请查看服务器下的透明数据加密设置。 

![支持“创建自己的密钥”的透明数据加密](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>使用 PowerShell 管理透明数据加密

若要通过 PowerShell 配置透明数据加密，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份进行连接。 

| Cmdlet | Description |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |为数据库启用或禁用透明数据加密|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |获取数据库的透明数据加密状态 |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |检查数据库的加密进度 |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |将 Key Vault 密钥添加到 SQL Server 实例 |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |获取 SQL Server 实例的 Key Vault 密钥  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |设置 SQL Server 实例的透明数据加密保护器 |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |获取透明数据加密保护器 |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |从 SQL Server 实例中删除 Key Vault 密钥 |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>使用 Transact-SQL 管理透明数据加密

使用 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名连接到数据库。

| 命令 | Description |
| --- | --- |
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF 会加密或解密数据库 |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |返回有关数据库的加密状态及其关联的数据库加密密钥的信息 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |返回有关每个数据仓库节点的加密状态及其关联的数据库加密密钥的信息 | 
|  | |

无法使用 Transact-SQL 将透明数据加密保护器切换为 Key Vault 中的密钥。 请使用 PowerShell 或 Azure 门户。

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>使用 REST API 管理透明数据加密
 
若要通过 REST API 配置透明数据加密，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份进行连接。 

| 命令 | Description |
| --- | --- |
|[创建或更新服务器](/rest/api/sql/servers/createorupdate)|将 Azure Active Directory 标识添加到 SQL Server 实例（用于授予 Key Vault 的访问权限）|
|[创建或更新服务器密钥](/rest/api/sql/serverkeys/createorupdate)|将 Key Vault 密钥添加到 SQL Server 实例|
|[删除服务器密钥](/rest/api/sql/serverkeys/delete)|从 SQL Server 实例中删除 Key Vault 密钥|
|[获取服务器密钥](/rest/api/sql/serverkeys/get)|从 SQL Server 实例中获取特定的 Key Vault 密钥|
|[按服务器列出服务器密钥](/rest/api/sql/serverkeys/listbyserver)|获取 SQL Server 实例的 Key Vault 密钥 |
|[创建或更新加密保护器](/rest/api/sql/encryptionprotectors/createorupdate)|设置 SQL Server 实例的透明数据加密保护器|
|[获取加密保护器](/rest/api/sql/encryptionprotectors/get)|获取 SQL Server 实例的透明数据加密保护器|
|[按服务器列出加密保护器](/rest/api/sql/encryptionprotectors/listbyserver)|获取 SQL Server 实例的透明数据加密保护器 |
|[创建或更新透明数据加密配置](/rest/api/sql/transparentdataencryptions/createorupdate)|为数据库启用或禁用透明数据加密|
|[获取透明数据加密配置](/rest/api/sql/transparentdataencryptions/get)|获取数据库的透明数据加密配置|
|[列出透明数据加密配置结果](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|获取数据库的加密结果|

## <a name="next-steps"></a>后续步骤

- 有关透明数据加密的一般说明，请参阅 [透明数据加密]((https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption)。
- 有关 SQL 数据库和数据仓库的支持“创建自己的密钥”的透明数据加密的详细信息，请参阅[支持“创建自己的密钥”的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。
- 若要开始使用支持“创建自己的密钥”的透明数据加密，请参阅操作方法指南[通过 PowerShell 使用 Key Vault 中的自有密钥启用透明数据加密](transparent-data-encryption-byok-azure-sql-configure.md)。
- 有关 Key Vault 的详细信息，请参阅 [Key Vault 文档页](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。
