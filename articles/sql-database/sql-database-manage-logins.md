---
title: 使用登录名和用户帐户授权服务器和数据库访问
description: 了解 Azure SQL 数据库和 Azure 突触分析如何使用登录名和用户帐户对用户进行访问进行身份验证。 还了解如何数据库角色和显式权限来授权登录和用户执行操作和查询数据。
keywords: sql 数据库安全,数据库安全管理,登录安全,数据库安全,数据库访问权限
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124823"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>使用登录名和用户帐户授权经过身份验证的用户访问 SQL 数据库和 Azure 同步分析

在本文中，您将了解：

- 用于配置 Azure SQL 数据库和 Azure 突触分析（以前的 Azure SQL 数据仓库）的选项，使用户能够执行管理任务并访问存储在这些数据库中的数据。
- 最初创建新 Azure SQL 数据库后的访问和授权配置
- 如何在主数据库和用户帐户中添加登录名和用户帐户，然后授予这些帐户管理权限
- 如何添加用户数据库中的用户帐户（与登录关联或作为包含的用户帐户）
- 使用数据库角色和显式权限配置具有用户数据库中权限的用户帐户

> [!IMPORTANT]
> 在本文的其余部分中，Azure SQL 数据库和 Azure 突触分析（以前的 Azure SQL 数据仓库）中的数据库统称为数据库或 Azure SQL（为了简单起见）。

## <a name="authentication-and-authorization"></a>身份验证和授权

[**身份验证**](sql-database-security-overview.md#authentication)是证明用户是他们声称是谁的过程。 用户使用用户帐户连接到数据库。
当用户尝试连接到数据库时，他们提供用户帐户和身份验证信息。 使用以下两种身份验证方法之一对用户进行身份验证：

- [SQL 身份验证](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)。

  使用此身份验证方法，用户提交用户帐户名称和相关密码以建立连接。 此密码存储在主数据库中，用于链接到登录名或存储在数据库中的用户帐户，其中包含未链接到登录的用户帐户的用户帐户。
- [Azure 活动目录身份验证](sql-database-aad-authentication.md)

  使用此身份验证方法，用户提交用户帐户名称，并要求服务使用存储在 Azure 活动目录中的凭据信息。

**登录和用户**：在 Azure SQL 中，数据库中的用户帐户可以与存储在主数据库中的登录名相关联，也可以是存储在单个数据库中的用户名。

- **登录**名是主数据库中的单个帐户，可以链接到一个或多个数据库中的用户帐户。 使用登录名，用户帐户的凭据信息将随登录一起存储。
- **用户帐户**是任何数据库中可能但不必链接到登录的单个帐户。 对于未链接到登录的用户帐户，凭据信息将随用户帐户一起存储。

[**Authorization**](sql-database-security-overview.md#authorization)使用数据库角色和显式权限管理访问数据和执行各种操作的授权。 授权是指分配给用户的权限，并确定允许用户做什么。 授权由用户帐户的数据库[角色成员身份](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)和[对象级权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)控制。 作为最佳实践，应向用户授予所需的最低权限。

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>创建新数据库后的现有登录名和用户帐户

创建第一个 Azure SQL 部署时，会为该登录名指定管理员登录名和关联的密码。 此管理帐户称为**服务器管理员**。在部署期间，主数据库和用户数据库中的登录名和用户进行以下配置：

- 使用指定的登录名创建具有管理权限的 SQL 登录名。 [登录](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)名是登录到 SQL 数据库的单个用户帐户。
- 此登录作为[服务器级主体](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)被授予所有数据库的完整管理权限。 此登录具有 SQL 数据库中的所有可用权限，并且不能受到限制。 在托管实例中，此登录将添加到[sysadmin 固定服务器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)（单个或池数据库不存在此角色）。
- 为每个[user account](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users)用户数据库中的`dbo`此登录创建调用的用户帐户。 [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)用户具有数据库中的所有数据库权限，并映射到`db_owner`固定数据库角色。 本文稍后将讨论其他固定数据库角色。

要标识数据库的管理员帐户，请打开 Azure 门户，然后导航到服务器或托管实例的 **"属性**"选项卡。

![SQL 服务器管理员](media/sql-database-manage-logins/sql-admins.png)

![SQL 服务器管理员](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> 创建管理员登录名后无法更改。 要重置逻辑服务器管理员的密码，请转到 Azure[门户](https://portal.azure.com)，单击**SQL 服务器**，从列表中选择服务器，然后单击"**重置密码**"。 要重置托管实例服务器的密码，请转到 Azure 门户，单击实例，然后单击 **"重置密码**"。 您还可以使用 PowerShell 或 Azure CLI。

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>创建其他登录名和具有管理权限的用户

此时，Azure SQL 实例仅配置为使用单个 SQL 登录名和用户帐户进行访问。 要创建具有完全或部分管理权限的其他登录名，请提供以下选项（具体取决于部署模式）：

- **创建具有完全管理权限的 Azure 活动目录管理员帐户**

  启用 Azure 活动目录身份验证并创建 Azure AD 管理员登录名。 一个 Azure 活动目录帐户可以配置为具有完全管理权限的 SQL 数据库部署的管理员。 此帐户可以是个人或安全组帐户。 如果要使用 Azure AD 帐户连接到 SQL 数据库，**则必须**配置 Azure AD 管理员。 有关为所有 SQL 数据库部署类型启用 Azure AD 身份验证的详细信息，请参阅以下文章：

  - [使用 Azure Active Directory 身份验证进行 SQL 身份验证](sql-database-aad-authentication.md)
  - [使用 SQL 配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)

- **在托管实例部署中，创建具有完全管理权限的 SQL 登录**

  - 在托管实例中创建其他 SQL Server 登录名
  - 使用[ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)语句将登录添加到[系统管理员固定服务器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)。 此登录将具有完全的管理权限。
  - 或者，使用<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">创建登录</a>语法创建 Azure AD[登录名](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi)。

- **在单个或池部署中，创建具有有限管理权限的 SQL 登录**

  - 为单个或池数据库部署或托管实例部署在主数据库中创建其他 SQL 登录
  - 在主数据库中创建与此新登录关联的用户帐户
  - 使用[ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) `loginmanager`语句将用户帐户添加到`dbmanager`、`master`角色或数据库中的两者（对于 Azure Synapse 分析，请使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)语句）。

  > [!NOTE]
  > `dbmanager`角色`loginmanager`与托管实例部署**无关**。

  这些用于单个或池数据库[的特殊主数据库角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-)的成员使用户可以有权创建和管理数据库或创建和管理登录。 在由`dbmanager`作为角色成员的用户创建的数据库中，成员映射到`db_owner`固定数据库角色，并可以使用`dbo`用户帐户登录和管理该数据库。 这些角色在主数据库之外没有显式权限。

  > [!IMPORTANT]
  > 您不能在单个或池数据库中创建具有完全管理权限的其他 SQL 登录。

## <a name="create-accounts-for-non-administrator-users"></a>为非管理员用户创建帐户

您可以使用两种方法之一为非管理用户创建帐户：

- **创建登录名**

  在主数据库中创建 SQL 登录名。 然后在每个数据库中创建一个用户帐户，该用户需要访问该用户帐户，并将用户帐户与该登录名相关联。 当用户必须访问多个数据库并且您希望保持密码同步时，此方法是首选。 但是，当与异地复制一起使用时，此方法非常复杂，因为登录必须在主服务器和辅助服务器上创建。 有关详细信息，请参阅[配置和管理 Azure SQL 数据库安全性，以便进行异地还原或故障转移](sql-database-geo-replication-security-config.md)。
- **创建用户帐户**

  在数据库中创建用户需要访问的用户帐户（也称为[包含用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)）。

  - 使用单个或池数据库，您始终可以创建这种类型的用户帐户。
  - 使用不支持[Azure AD 服务器主体](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)的托管实例数据库，只能在[包含的数据库中](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)创建这种类型的用户帐户。 使用支持[Azure AD 服务器主体](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)的托管实例，您可以创建用户帐户以对托管实例进行身份验证，而无需将数据库用户创建为包含的数据库用户。

  使用此方法，用户身份验证信息存储在每个数据库中，并自动复制到异地复制的数据库。 但是，如果同一帐户存在于多个数据库中，并且您使用的是 SQL 身份验证，则必须手动保持密码同步。 此外，如果用户在不同的数据库中具有不同的密码，记住这些密码可能会成为一个问题。

> [!IMPORTANT]
> 要创建映射到 Azure AD 标识的包含用户，必须使用 SQL 数据库中的管理员的 Azure AD 帐户登录。 在托管实例中，具有`sysadmin`权限的 SQL 登录名也可以创建 Azure AD 登录名或用户。

有关如何创建登录名和用户的示例，请参阅：

- [为单个或池数据库创建登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [为托管实例数据库创建登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [为 Azure 突触分析数据库创建登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [创建用户](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [创建 Azure AD 包含的用户](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> 有关安全教程，其中包括在单个或池数据库中创建 SQL Server 包含的用户，请参阅[教程：保护单个或池数据库](sql-database-security-tutorial.md)。

## <a name="using-fixed-and-custom-database-roles"></a>使用固定和自定义数据库角色

在数据库中创建用户帐户后（基于登录名或作为包含用户）后，可以授权该用户执行各种操作并访问特定数据库中的数据。 您可以使用以下方法授权访问：

- **固定的数据库角色**

  将用户帐户添加到[固定数据库角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)。 有 9 个固定数据库角色，每个角色都有一组定义的权限。 最常见的固定数据库角色是：db_owner、db_ddladmin、db_datawriter、db_datareader、db_denydatawriter和**db_denydatawriter****db_denydatareader。** **db_owner** **db_ddladmin** **db_datawriter** **db_datareader** **db_owner** 通常用于向部分用户授予完全权限。 其他固定数据库角色可用于快速开发简单的数据库，但不建议用于大多数生产数据库。 例如 **，db_datareader**固定数据库角色授予对数据库中每个表的读取访问权限，这比绝对必要的权限要大。

  - 要将用户添加到固定数据库角色：

    - 在 Azure SQL 数据库中，使用[ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)语句。 有关示例，请参阅[ALTER ROLE 示例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure 同步分析，使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)语句。 有关示例，请参阅[sp_addrolemember示例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)。

- **自定义数据库角色**

  使用[CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)语句创建自定义数据库角色。 自定义角色使您能够创建自己的用户定义的数据库角色，并仔细为每个角色授予业务需求所需的最少权限。 然后，您可以将用户添加到自定义角色。 如果用户是多个角色的成员，则会聚合所有这些角色的权限。
- **直接授予权限**

  直接授予用户帐户[权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)。 可以在 SQL 数据库中单独授予或拒绝 100 多种权限。 这些权限中，许多都是嵌套式的。 例如，针对架构的 `UPDATE` 权限包括针对该架构中每个表的 `UPDATE` 权限。 与大多数权限系统中的情况一样，拒绝某个权限将覆盖对该权限的授予操作。 考虑到权限的嵌套性质和数目，可能需要进行仔细的研究才能设计出适当的权限系统，以便对数据库进行恰当的保护。 一开始可以了解[权限（数据库引擎）](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)中的权限列表，并查看这些权限的[海报大小的图](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)。

## <a name="using-groups"></a>使用组

高效访问管理使用分配给 Active Directory 安全组的权限以及固定或自定义角色，而不是单个用户。

- 使用 Azure 活动目录身份验证时，将 Azure 活动目录用户放入 Azure 活动目录安全组。 为该组创建包含数据库用户。 将一个或多个数据库用户放入具有适合该用户组的特定权限的自定义数据库角色中。

- 使用 SQL 身份验证时，在数据库中创建包含的数据库用户。 将一个或多个数据库用户放入具有适合该用户组的特定权限的自定义数据库角色中。

  > [!NOTE]
  > 您还可以将组用于非包含的数据库用户。

用户应熟悉以下功能，以便限制或提升权限：

- [模拟](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)和[模块签名](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)可用于安全地暂时提升权限。
- [行级安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)可用于限制用户可访问的行。
- [数据屏蔽](sql-database-dynamic-data-masking-get-started.md)可用于限制敏感数据的公开。
- [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)可用于限制可对数据库执行的操作。

## <a name="next-steps"></a>后续步骤

有关所有 SQL 数据库安全功能的概述，请参阅 [SQL 安全概述](sql-database-security-overview.md)。
