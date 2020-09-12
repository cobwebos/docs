---
title: 使用登录名和用户帐户授权服务器和数据库访问
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: 了解 Azure SQL 数据库、SQL 托管实例和 Azure Synapse 如何对使用登录名和用户帐户进行访问的用户进行身份验证。 并了解如何通过授权数据库角色和显式权限，来授权登录名和用户，以执行操作和查询数据。
keywords: sql 数据库安全,数据库安全管理,登录安全,数据库安全,数据库访问权限
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 891d5907ee8c964ebe7e281f6298205712ce1186
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441164"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>向数据库授予对 Azure SQL 数据库、SQL 托管实例和 Azure Synapse Analytics 的访问权限
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

本文介绍：

- 用于将 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 配置 (以前的 SQL 数据仓库) 的选项，使用户能够执行管理任务并访问存储在这些数据库中的数据。
- 最初新建服务器后的访问权限和授权配置。
- 如何在 master 数据库和用户账户中添加登录名和用户帐户，然后为这些帐户授予管理权限。
- 如何在用户数据库中添加用户帐户（与登录名关联或作为包含的用户帐户）。
- 使用数据库角色和显式权限配置在用户数据库中拥有权限的用户帐户。

> [!IMPORTANT]
> 本文其余部分将 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse 中的数据库统称为数据库，并且服务器指的是为 Azure SQL 数据库和 Azure Synapse 管理数据库的[服务器](logical-servers.md)。

## <a name="authentication-and-authorization"></a>身份验证和授权

[身份验证](security-overview.md#authentication)是证明用户所声明身份的过程。 某个用户使用用户帐户连接到数据库。
当用户尝试连接到数据库时，需要提供用户帐户和身份验证信息。 将使用以下两种身份验证方法之一对用户进行身份验证：

- [SQL 身份验证](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)。

  使用这种身份验证方法时，用户通过提交用户帐户名和关联的密码来建立连接。 对于与登录名关联的用户帐户，此密码存储在 master 数据库中；或对于没有与登录名关联的用户帐户，此密码存储在包含这一类用户帐户的数据库中。
- [Azure Active Directory 身份验证](authentication-aad-overview.md)

  使用此身份验证方法时，用户将提交用户帐户名，并请求服务使用 Azure Active Directory (Azure AD) 中存储的凭据信息。

**登录名和用户**：数据库中的用户帐户可以与 master 数据库中存储的登录名相关联，也可以是单个数据库中存储的用户名。

- 登录名是 master 数据库中的单个帐户，一个或多个数据库中的用户帐户可以关联到该帐户。 使用登录名时，用户帐户的凭据信息将与该登录名一起存储。
- 用户帐户是任何数据库中的单个帐户，该帐户可以但不一定要关联到登录名。 使用未关联到登录名的用户帐户时，凭据信息将与该用户帐户一起存储。

访问数据和执行各种操作所需的[授权](security-overview.md#authorization)是使用数据库角色和显式权限进行管理的。 授权是指为用户分配权限，它决定了该用户可以执行哪些操作。 授权是由用户帐户的数据库[角色成员身份](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)和[对象级权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)控制。 作为最佳实践，应向用户授予所需的最低权限。

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>创建新数据库后的现有登录名和用户帐户

首次部署 Azure SQL 时，需为该登录名指定管理员登录名和关联的密码。 此管理帐户称为“服务器管理员”。在部署期间，将在 master 数据库和用户数据库中进行以下登录名和用户配置：

- 使用指定的登录名创建拥有管理特权的 SQL 登录名。 [登录名](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)是用于登录到 SQL 数据库、SQL 托管实例和 Azure Synapse 的单个用户帐户。
- 此登录名对所有数据库拥有完全管理权限，充当[服务器级主体](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)。 此登录名具有所有可用权限，且不受限制。 在 SQL 托管实例中，会将此登录名添加到 [sysadmin 固定服务器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)（Azure SQL 数据库中不存在此角色）。
- 将在每个用户数据库中为此登录名创建名为 `dbo` 的[用户帐户](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users)。 [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) 用户在数据库中拥有所有数据库权限，将映射到 `db_owner` 固定数据库角色。 本文稍后将讨论其他固定数据库角色。

若要确定数据库的管理员帐户，请打开 Azure 门户，然后导航到服务器或托管实例的“属性”选项卡。

![SQL Server 管理员](./media/logins-create-manage/sql-admins.png)

![SQL Server 管理员](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> 创建管理员登录名后，无法对其进行更改。 若要重置服务器管理员的密码，请转到 [Azure 门户](https://portal.azure.com)，单击“SQL Server”，从列表中选择服务器，然后单击“重置密码” 。 若要重置 SQL 托管实例的密码，请转到 Azure 门户，单击该实例，然后单击“重置密码”。 也可以使用 PowerShell 或 Azure CLI。

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>创建拥有管理权限的其他登录名和用户

此时，只是配置了使用单个 SQL 登录名和用户帐户来访问服务器或托管实例。 若要创建其他拥有完全管理权限或部分管理权限的登录名，可使用以下选项（取决于部署模式）：

- **创建拥有完全管理权限的 Azure Active Directory 管理员帐户**

  启用 Azure Active Directory 身份验证并创建 Azure AD 管理员登录名。 可将一个 Azure Active Directory 帐户配置为拥有完全管理权限的 Azure SQL 部署管理员。 此帐户可以是个人帐户或安全组帐户。 若要使用 Azure AD 帐户连接到 SQL 数据库、SQL 托管实例或 Azure Synapse，“必须”配置 Azure AD 管理员。 有关为所有 Azure SQL 部署类型启用 Azure AD 身份验证的详细信息，请参阅以下文章：

  - [使用 Azure Active Directory 身份验证进行 SQL 身份验证](authentication-aad-overview.md)
  - [使用 SQL 配置和管理 Azure Active Directory 身份验证](authentication-aad-configure.md)

- **在 SQL 托管实例中，创建拥有完全管理权限的 SQL 登录名**

  - 在 master 数据库中再创建一个 SQL 登录名。
  - 使用 [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) 语句将登录名添加到 [sysadmin 固定服务器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)。 此登录名拥有完全管理权限。
  - 或者，使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 语法创建 [Azure AD 登录名](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。

- **在 SQL 数据库中，创建具有有限管理权限的 SQL 登录名**

  - 在 master 数据库中再创建一个 SQL 登录名。
  - 在 master 数据库中创建与此新登录名关联的用户帐户。
  - 在 `master` 数据库中，使用 [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) 语句（对于 Azure Synapse，请使用 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 语句）将用户帐户添加到 `dbmanager` 和/或 `loginmanager` 角色。

  > [!NOTE]
  > `dbmanager` 和 `loginmanager` 角色与 SQL 托管实例部署“不”相关。

  Azure SQL 数据库的这些[特殊 master 数据库角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-)的成员有权创建和管理数据库或登录名。 在属于 `dbmanager` 角色的成员的用户创建的数据库中，该成员将映射到 `db_owner` 固定数据库角色，并可以使用 `dbo` 用户帐户登录到该数据库以及对其进行管理。 这些角色在 master 数据库外部没有显式权限。

  > [!IMPORTANT]
  > 无法在 SQL 数据库中创建拥有完全管理权限的其他 SQL 登录名。

## <a name="create-accounts-for-non-administrator-users"></a>为非管理员用户创建帐户

可使用以下两种方法之一为非管理用户创建帐户：

- **创建登录名**

  在 master 数据库中创建 SQL 登录名。 然后在用户需要访问的每个数据库中创建一个用户帐户，并将该用户帐户关联到该登录名。 如果用户必须访问多个数据库，而你希望密码保持同步，则此方法是首选方法。 但是，在与异地复制结合使用时，此方法会很复杂，因为必须同时在主服务器和辅助服务器上创建登录名。 有关详细信息，请参阅[针对异地还原或故障转移配置和管理 Azure SQL 数据库的安全性](active-geo-replication-security-configure.md)。
- **创建用户帐户**

  在用户需要访问的数据库中创建用户帐户（也称为[包含的用户](/sql/relational-databases/security/contained-database-users-making-your-database-portable)）。

  - 对于 SQL 数据库，始终可以创建这种类型的用户帐户。
  - 对于支持 [Azure AD 服务器主体](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)的 SQL 托管实例，可以创建用户帐户来对 SQL 托管实例进行身份验证，而无需创建用作包含的数据库用户的数据库用户。

  使用此方法时，用户身份验证信息将存储在每个数据库中，并自动复制到异地复制的数据库。 但是，如果同一帐户存在于多个数据库中，并且你使用的是 Azure SQL 身份验证，则必须手动保持密码同步。 此外，如果用户在不同的数据库中具有密码不同的帐户，则可能会很难记住这些密码。

> [!IMPORTANT]
> 若要创建映射到 Azure AD 标识的包含的用户，必须使用充当 Azure SQL 数据库中数据库管理员的 Azure AD 帐户登录。 在 SQL 托管实例中，拥有 `sysadmin` 权限的 SQL 登录也可以创建 Azure AD 登录名或用户。

有关演示如何创建登录名和用户的示例，请参阅：

- [创建 Azure SQL 数据库的登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [创建 Azure SQL 托管实例的登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [创建 Azure Synapse 的登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [创建用户](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [创建 Azure AD 包含的用户](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> 如需查看安全教程，包括如何在 Azure SQL 数据库中创建用户，请参阅[教程：保护 Azure SQL 数据库](secure-database-tutorial.md)。

## <a name="using-fixed-and-custom-database-roles"></a>使用固定数据库角色和自定义数据库角色

在数据库中创建用户帐户后，可以授权该用户基于登录名或以包含的用户身份执行各种操作及访问特定数据库中的数据。 可使用以下方法来授权访问：

- 固定数据库角色

  将用户帐户添加到[固定数据库角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)。 有 9 个固定数据库角色，每个角色拥有一组定义的权限。 最常用的固定数据库角色为：db_owner、db_ddladmin、db_datawriter、db_datareader、db_denydatawriter 和 db_denydatareader。      **db_owner** 通常用于向部分用户授予完全权限。 其他固定数据库角色可用于快速开发简单的数据库，但不建议用于大多数生产数据库。 例如，db_datareader 固定数据库角色授予用户对数据库中每个表的读取访问权限，这超出了必要的范畴。

  - 若要将用户添加到固定数据库角色：

    - 在 Azure SQL 数据库中使用 [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) 语句。 有关示例，请参阅 [ALTER ROLE 示例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - 在 Azure Synapse 中，请使用 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 语句。 有关示例，请参阅 [sp_addrolemember 示例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)。

- **自定义数据库角色**

  使用 [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) 语句创建自定义数据库角色。 使用自定义角色可以创建自己的用户定义数据库角色，并根据业务需求慎重地向每个角色授予所需的最低权限。 然后，可将用户添加到自定义角色。 如果用户是多个角色的成员，则会聚合所有这些角色的权限。
- **直接授予权限**

  直接为用户帐户授予[权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)。 可以在 SQL 数据库中单独授予或拒绝 100 多种权限。 这些权限中，许多都是嵌套式的。 例如，针对架构的 `UPDATE` 权限包括针对该架构中每个表的 `UPDATE` 权限。 与大多数权限系统中的情况一样，拒绝某个权限将覆盖对该权限的授予操作。 考虑到权限的嵌套性质和数目，可能需要进行仔细的研究才能设计出适当的权限系统，以便对数据库进行恰当的保护。 一开始可以了解[权限（数据库引擎）](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)中的权限列表，然后查看这些权限的[海报大小的图](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)。

## <a name="using-groups"></a>使用组

有效的访问管理使用分配给 Active Directory 安全组以及固定角色或自定义角色的权限，而不使用分配给个人用户的权限。

- 使用 Azure Active Directory 身份验证时，请将 Azure Active Directory 用户放入 Azure Active Directory 安全组。 为该组创建包含数据库用户。 将一个或多个数据库用户添加为自定义或内置数据库角色的成员，这些角色具有对于该组用户而言合适的特定权限。

- 使用 SQL 身份验证时，请在数据库中创建包含的数据库用户。 将一个或多个数据库用户放入到对该用户组拥有特定适当权限的自定义数据库角色。

  > [!NOTE]
  > 也可以对非包含数据库用户使用组。

应该自行熟悉一下下述能够用于限制或提升权限的功能：

- [模拟](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)和[模块签名](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)可用于安全地暂时提升权限。
- [行级安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 可用于限制用户可访问的行。
- [数据屏蔽](dynamic-data-masking-overview.md)可用于限制敏感数据的公开。
- [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 可用于限制可对数据库执行的操作。

## <a name="next-steps"></a>后续步骤

有关所有 Azure SQL 数据库和 SQL 托管实例安全功能的概述，请参阅[安全概述](security-overview.md)。
