---
title: 保护数据库
description: 在 SQL Analytics 的 SQL 池资源中保护数据库和开发解决方案的技巧。
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 89ec405a348e3ace851fd5f5e17283a8036692a5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199404"
---
# <a name="secure-a-database-in-azure-synapse"></a>保护 Azure Synapse 中的数据库
> [!div class="op_single_selector"]
> * [安全概述](sql-data-warehouse-overview-manage-security.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [加密（门户）](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

本文将指导你了解有关在 SQL Analytics 中保护 SQL 池的基本知识。 具体而言，本文将介绍如何在使用 SQL 池设置的数据库中限制访问、保护数据和监视活动。

## <a name="connection-security"></a>连接安全性
连接安全性是指如何使用防火墙规则和连接加密来限制和保护数据库连接。

服务器和数据库使用防火墙规则来拒绝来自尚未显式列入允许列表的 IP 地址的连接尝试。 若要从应用程序或客户端计算机的公共 IP 地址进行连接，必须先使用 Azure 门户、REST API 或 PowerShell 创建服务器级防火墙规则。 

作为最佳实践，应该尽量通过服务器防火墙来限制允许的 IP 地址范围。  若要从本地计算机访问 SQL 池，请确保网络和本地计算机上的防火墙允许 TCP 端口1433上的传出通信。  

Azure Synapse Analytics 使用服务器级 IP 防火墙规则。 它不支持数据库级 IP 防火墙规则。 有关详细信息，请参阅[AZURE SQL 数据库防火墙规则](../sql-database/sql-database-firewall-configure.md)

默认情况下，加密到 SQL 池的连接。  将忽略通过修改连接设置禁用加密的操作。

## <a name="authentication"></a>Authentication
身份验证是指连接到数据库时如何证明身份。 SQL 池目前支持使用用户名和密码进行 SQL Server 身份验证，并且具有 Azure Active Directory。 

在为数据库创建逻辑服务器时，已指定一个包含用户名和密码的“服务器管理员”登录名。 使用这些凭据，可以通过 SQL Server 身份验证以数据库所有者（或“dbo”）的身份在该服务器对任何数据库进行验证。

但是，组织的用户最好使用不同的帐户进行验证。 这样，便可以限制授予应用程序的权限，并在应用程序代码容易受到 SQL 注入攻击的情况下降低恶意活动的风险。 

若要创建 SQL Server 验证的用户，请使用服务器管理员登录名连接到服务器上的 **master** 数据库，并创建新的服务器登录名。  最好还在 master 数据库中创建一个用户。 在 master 中创建用户以后，用户即可使用 SSMS 之类的工具登录，不需指定数据库名称。  此外，用户还可以使用对象资源管理器查看 SQL Server 上的所有数据库。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

然后，使用服务器管理员登录名连接到**SQL 池数据库**，并根据所创建的服务器登录名创建数据库用户。

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

要授予用户执行其他操作（例如创建登录名或新数据库）的权限，则还需在 master 数据库中为其分配 `Loginmanager` 和 `dbmanager` 角色。 

有关这些其他角色以及对 SQL 数据库进行身份验证的详细信息，请参阅[在 AZURE Sql 数据库中管理数据库和登录名](../sql-database/sql-database-manage-logins.md)。  有关使用 Azure Active Directory 进行连接的详细信息，请参阅[使用 Azure Active Directory 身份验证进行连接](sql-data-warehouse-authentication.md)。

## <a name="authorization"></a>授权
授权是指在经过身份验证和连接后可以在数据库中执行的操作。 授权权限由角色成员资格和权限决定。 作为最佳实践，应向用户授予所需的最低权限。 可使用下列存储过程来管理角色：

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

用于连接的服务器管理员帐户是 db_owner 所有者的成员，该帐户有权在数据库中执行任何操作。 请保存此帐户，以便部署架构升级并执行其他管理操作。 权限受到更多限制的“ApplicationUser”帐户可让用户使用应用程序所需的最低权限从应用程序连接到数据库。

有多种方法可以进一步限制用户可在数据库中执行的操作：

* 精细[权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15)可让你控制可以对数据库中的单个列、表、视图、架构、过程和其他对象执行的操作。 使用细化的权限可以进行最精细的控制，可以根据用户需要授予其最低权限。 
* 除 db_datareader 和 db_datawriter 以外的[数据库角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15)可用于创建权限较大的应用程序用户帐户或权限较小的管理帐户。 内置的固定的数据库角色可以方便地用来授予权限，但可能会导致所授权限超出需要的情况。
* [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15)可用于限制可对数据库执行的操作。

下面的示例介绍如何对用户定义的构架授予读取访问权限。
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

从 Azure 门户或使用 Azure 资源管理器 API 管理数据库和逻辑服务器的操作会根据门户用户帐户的角色分配进行控制。 有关详细信息，请参阅[Azure 门户中基于角色的访问控制](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure)。

## <a name="encryption"></a>加密
透明数据加密（TDE）可加密和解密静态数据，从而帮助防止恶意活动的威胁。 在加密数据库时，可以对关联的备份和事务日志文件加密，无需对应用程序进行任何更改。 TDE 使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。 

在 SQL 数据库中，数据库加密密钥由内置服务器证书保护。 内置服务器证书对每个 SQL 数据库服务器都是唯一的。 Microsoft 至少每 90 天自动轮换这些证书。 使用的加密算法为 AES-256。 有关 TDE 的一般描述，请参阅[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15)。

可以使用[Azure 门户](sql-data-warehouse-encryption-tde.md)或[t-sql](sql-data-warehouse-encryption-tde-tsql.md)加密数据库。

## <a name="next-steps"></a>后续步骤
有关如何用不同的协议连接到仓库的详细信息和示例，请参阅[连接到 SQL 池](sql-data-warehouse-connect-overview.md)。
