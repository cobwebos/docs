---
title: 透明数据加密
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 的透明数据加密概述。 本文档介绍服务托管的透明数据加密和“创建自己的密钥”的优势与配置选项。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 06/15/2020
ms.openlocfilehash: d9bc5e91d45b75c47cee31c45b937f7d3f0118b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836677"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 的透明数据加密
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[透明数据加密 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) 通过加密静态数据，帮助保护 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 免受恶意脱机活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。 对于所有新部署的 SQL 数据库，默认已启用 TDE；对于 Azure SQL 数据库、Azure SQL 托管实例的旧版数据库，需要手动启用 TDE。 对于 Azure Synapse Analytics，需要手动启用 TDE。

TDE 对页面级数据执行实时 I/O 加密和解密。 将每个页面读入内存时会将其解密，在写入磁盘之前会将其加密。 TDE 使用称为数据库加密密钥 (DEK) 的对称密钥加密整个数据库的存储。 在数据库启动时，加密的 DEK 将被解密，然后用于解密和重新加密 SQL Server 数据库引擎进程中的数据库文件。 DEK 由 TDE 保护器保护。 TDE 保护器是服务托管的证书（服务托管的透明数据加密）或存储在 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) 中的非对称密钥（客户管理的透明数据加密）。

对于 Azure SQL 数据库和 Azure Synapse，TDE 保护器在[服务器](logical-servers.md)级别设置，并由该服务器关联的所有数据库继承。 对于 Azure SQL 托管实例，TDE 保护器在实例级别设置，并由该实例上所有加密的数据库继承。 除非另有说明，否则术语“服务器”在整个文档中指的是服务器和实例。

> [!IMPORTANT]
> 默认情况下，将使用服务托管的透明数据加密对 SQL 数据库中所有新建的数据库进行加密。 默认情况下，2017 年 5 月之前创建的现有 SQL 数据库以及通过还原、异地复制和数据库副本创建的 SQL 数据库均不加密。 默认情况下，2019 年 2 月之前创建的现有 SQL 托管实例数据库不加密。 通过源提供的还原继承加密状态创建的 SQL 托管实例数据库。

> [!NOTE]
> 不能使用 TDE 来加密 SQL 数据库中的 master 数据库。  **master** 数据库包含对用户数据库执行 TDE 操作时所需的对象。

## <a name="service-managed-transparent-data-encryption"></a>服务托管的透明数据加密

在 Azure 中，TDE 的默认设置是 DEK 受内置服务器证书保护。 内置服务器证书对于每个服务器都是唯一的，使用的加密算法是 AES 256。 如果某个数据库存在异地复制关系，则主数据库和异地辅助数据库将受主数据库的父服务器密钥保护。 如果两个数据库连接到同一个服务器，则它们也共享相同的内置证书。 Microsoft 根据内部安全策略自动轮换这些证书，根密钥由 Microsoft 内部密码存储保护。 客户可在 [Microsoft 信任中心](https://servicetrust.microsoft.com/)上的独立第三方审核报告中验证 sql 数据库和 sql 托管实例符合内部安全策略。

Microsoft 还可按需无缝移动和管理密钥，以实现异地复制和还原。

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>客户管理的透明数据加密 - 创建自己的密钥

客户管理的 TDE 也称为 TDE 的“创建自己的密钥”(BYOK) 支持。 在此方案中，用于加密 DEK 的 TDE 保护器是客户管理的非对称密钥，该密钥存储在客户自有且自行管理的 Azure Key Vault（Azure 的基于云的外部密钥管理系统）中，并且永远不会离开该密钥保管库。 TDE 保护 [程序可由密钥保管库生成，或者](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) 从本地硬件安全模块 (HSM) 设备传输到密钥保管库。 需要向 SQL 数据库、SQL 托管实例和 Azure Synapse 授予对客户管理的密钥保管库的权限才能对 DEK 进行解密和加密。 如果吊销了到密钥保管库的服务器的权限，则数据库将无法访问，并对所有数据进行加密

使用集成了 Azure Key Vault 的 TDE，用户可以控制密钥管理任务，包括密钥轮换、密钥保管库权限、密钥备份，以及使用 Azure Key Vault 功能对所有 TDE 保护器启用审核/报告。 Key Vault 提供了中心密钥管理，利用了严格监视的 Hsm，并允许在密钥和数据的管理之间分离职责，以帮助满足安全策略的符合性。
若要详细了解 Azure SQL 数据库和 Azure Synapse 的 BYOK，请参阅[透明数据加密与 Azure Key Vault 的集成](transparent-data-encryption-byok-overview.md)。

若要开始使用 TDE 与 Azure Key Vault 的集成，请参阅操作指南[使用 Key Vault 中的自有密钥启用透明数据加密](transparent-data-encryption-byok-configure.md)。

## <a name="move-a-transparent-data-encryption-protected-database"></a>移动受透明数据加密保护的数据库

对于 Azure 中的操作，不需要解密数据库。 源数据库或主数据库上 TDE 的设置均以透明方式继承在目标系统上。 包括的操作涉及到：

- 异地还原
- 自助时间点还原
- 还原已删除的数据库
- 活动异地复制
- 创建数据库副本
- 将备份文件还原到 Azure SQL 托管实例

> [!IMPORTANT]
> 由于无法访问用于加密的证书，因此不支持在 Azure SQL 托管实例中对由服务托管的 TDE 加密的数据库手动执行仅复制备份。 使用时间点还原功能将此类型的数据库移到另一个 SQL 托管实例或切换到客户托管的密钥。

在导出受 TDE 保护的数据库时，该数据库的导出内容不会加密。 此导出内容存储在未加密的 BACPAC 文件中。 完成新数据库的导入后，请务必适当保护 BACPAC 文件并启用 TDE。

例如，如果从 SQL Server 实例导出 BACPAC 文件，则新数据库的导入内容不会自动加密。 同样，如果将 BACPAC 文件导出到 SQL Server 实例，则新数据库也不会自动加密。

向/从 SQL 数据库导出数据库时例外。 在新数据库中启用了 TDE，但 BACPAC 文件本身仍未加密。

## <a name="manage-transparent-data-encryption"></a>管理透明数据加密

# <a name="the-azure-portal"></a>[Azure 门户](#tab/azure-portal)

在 Azure 门户中管理 TDE。

若要通过 Azure 门户配置 TDE，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份建立连接。

在数据库级别启用和禁用 TDE。 对于 Azure SQL 托管实例，请使用 Transact-SQL (T-SQL) 对数据库启用和禁用 TDE。 对于 Azure SQL 数据库和 Azure Synapse，可在使用 Azure 管理员或参与者帐户登录后，在 [Azure 门户网站](https://portal.azure.com)中管理数据库的 TDE。 在用户数据库下查找 TDE 设置。 默认会使用服务托管的透明数据加密。 将为包含数据库的服务器自动生成 TDE 证书。

![服务托管的透明数据加密](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

在服务器级别或实例级别设置 TDE 主密钥（称为 TDE 保护器）。 若要使用支持 BYOK 的 TDE，并使用 Key Vault 中的密钥来保护数据库，请在你的服务器下打开 TDE 设置。

![支持“创建自己的密钥”的透明数据加密](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 管理 TDE。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器模块，但是所有未来的开发都是针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要通过 PowerShell 配置 TDE，用户必须作为 Azure 所有者、参与者或 SQL 安全管理员进行连接。

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>用于 Azure SQL 数据库和 Azure Synapse 的 Cmdlet

对 Azure SQL 数据库和 Azure Synapse 使用以下 cmdlet：

| Cmdlet | 说明 |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |为数据库启用或禁用透明数据加密。|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |获取数据库的透明数据加密状态。 |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |检查数据库的加密进度。 |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |将 Key Vault 密钥添加到服务器。 |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |获取服务器的 Key Vault 密钥  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |为服务器设置透明数据加密保护器。 |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |获取透明数据加密保护器 |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |将 Key Vault 密钥从服务器删除。 |
|  | |

> [!IMPORTANT]
> 对于 Azure SQL 托管实例，请使用 T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) 命令在数据库级别启用和禁用 TDE，并查看[示例 PowerShell 脚本](transparent-data-encryption-byok-configure.md)以在实例级别管理 TDE。

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

使用 Transact-SQL 管理 TDE。

使用 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名连接到数据库。

| 命令 | 说明 |
| --- | --- |
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF 会加密或解密数据库 |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |返回有关数据库的加密状态及其关联的数据库加密密钥的信息 |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |返回有关每个 Azure Synapse 节点的加密状态及其关联的数据库加密密钥的信息 |
|  | |

不能使用 Transact-SQL 将 TDE 保护器切换到 Key Vault 中的密钥。 请使用 PowerShell 或 Azure 门户。

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

使用 REST API 管理 TDE。

若要通过 REST API 配置 TDE，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份建立连接。
对 Azure SQL 数据库和 Azure Synapse 使用以下命令集：

| 命令 | 说明 |
| --- | --- |
|[创建或更新服务器](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|向服务器添加 Azure Active Directory 标识。 （用于授予对 Key Vault 的访问权限）|
|[创建或更新服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|将 Key Vault 密钥添加到服务器。|
|[删除服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|将 Key Vault 密钥从服务器删除。 |
|[获取服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|从服务器获取特定的 Key Vault 密钥。|
|[按服务器列出服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|获取服务器的 Key Vault 密钥。 |
|[创建或更新加密保护器](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|为服务器设置 TDE 保护器。|
|[获取加密保护器](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|获取服务器的 TDE 保护器。|
|[按服务器列出加密保护器](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|获取服务器的 TDE 保护器。 |
|[创建或更新透明数据加密配置](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|启用或禁用数据库的 TDE。|
|[获取透明数据加密配置](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|获取数据库的 TDE 配置。|
|[列出透明数据加密配置结果](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|获取数据库的加密结果。|

## <a name="see-also"></a>另请参阅

- 在 Azure 虚拟机上运行的 SQL Server 也可以使用 Key Vault 中的非对称密钥。 配置步骤与在 SQL 数据库和 SQL 托管实例中使用非对称密钥不同。 有关详细信息，请参阅[使用 Azure Key Vault 进行可扩展的密钥管理 (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。
- 有关 TDE 的一般介绍，请参阅[透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption)。
- 若要详细了解 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse 的支持 BYOK 的 TDE，请参阅[支持“创建自己的密钥”的透明数据加密](transparent-data-encryption-byok-overview.md)。
- 若要开始使用支持“创建自己的密钥”的 TDE，请参阅操作指南[使用 Key Vault 中的自有密钥启用透明数据加密](transparent-data-encryption-byok-configure.md)。
- 有关 Key Vault 的详细信息，请参阅[保护对密钥保管库的访问](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。
