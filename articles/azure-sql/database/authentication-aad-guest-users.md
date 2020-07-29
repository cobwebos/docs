---
title: 创建 Azure AD 来宾用户
description: 如何创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员，而无需使用 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 中的 Azure AD 组
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: b3abda9787654734b24cc27987921ef40d8070fc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293996"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 本文是**公开预览版**。

Azure Active Directory （Azure AD）中的来宾用户是从其他 Azure Active Directory 或其外部导入到当前 Azure AD 中的用户。 例如，来宾用户可以包含来自其他 Azure Active directory 的用户，也可以包含* \@ outlook.com*、 * \@ hotmail.com*、 * \@ live.com*或* \@ gmail.com*等帐户。 本文将演示如何创建 Azure AD guest 用户，并将该用户设置为 Azure SQL 逻辑服务器的 Azure AD 管理员，而无需让该来宾用户成为 Azure AD 内组的一部分。

## <a name="feature-description"></a>功能描述

此功能将当前限制提升为在 Azure AD 中创建的组的成员时，仅允许来宾用户连接到 Azure SQL 数据库、SQL 托管实例或 Azure Synapse Analytics。 需要在给定的数据库中使用[CREATE user （transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)语句将组映射到用户。 为包含来宾用户的 Azure AD 组创建数据库用户后，来宾用户可以使用具有 MFA 身份验证的 Azure Active Directory 登录到数据库。 作为此**公共预览版**的一部分，可以创建来宾用户并直接连接到 sql 数据库、sql 托管实例或 Azure Synapse，而无需先将其添加到 Azure AD 组，然后为该 Azure AD 组创建数据库用户。

作为此功能的一部分，你还可以将 Azure AD guest 用户直接设置为 Azure SQL 逻辑服务器的 AD 管理员。 现有功能，其中 guest 用户可以是 Azure AD 组的一部分，然后可以将该组设置为 Azure SQL 逻辑服务器的 Azure AD 管理员不受影响。 作为 Azure AD 组一部分的数据库中的来宾用户也不受此更改的影响。

有关使用 Azure AD 组对来宾用户的现有支持的详细信息，请参阅[使用多重身份验证 Azure Active Directory 身份验证](authentication-mfa-ssms-overview.md)。

## <a name="prerequisite"></a>先决条件

- 使用 PowerShell 将来宾用户设置为 Azure SQL 逻辑服务器的 Azure AD 管理员时，需要[Az 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)模块或更高版本。

## <a name="create-database-user-for-azure-ad-guest-user"></a>创建 Azure AD guest 用户的数据库用户 

按照以下步骤创建使用 Azure AD guest 用户的数据库用户。

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>在 SQL 数据库和 Azure Synapse 中创建来宾用户

1. 确保已将来宾用户（例如 `user1@gmail.com` ）添加到 Azure AD 中，并为数据库服务器设置了 Azure AD 管理员。 Azure Active Directory 身份验证需要具有 Azure AD 管理员身份验证。

1. 以 Azure AD 管理员身份或具有足够的 SQL 权限的 Azure AD 用户身份连接到 SQL 数据库，以创建用户，并在需要添加来宾用户的数据库上运行以下命令：

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 现在应会为来宾用户创建数据库用户 `user1@gmail.com` 。

1. 运行以下命令，验证已成功创建数据库用户：

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. 使用 `user1@gmail.com` [SQL SERVER MANAGEMENT STUDIO （SSMS）](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)通过使用身份验证方法**Azure Active Directory-通用**的身份验证方法，以来宾用户身份断开和登录到数据库。 有关详细信息，请参阅[使用多重身份验证 Azure Active Directory 身份验证](authentication-mfa-ssms-overview.md)。

### <a name="create-guest-user-in-sql-managed-instance"></a>在 SQL 托管实例中创建来宾用户

> [!NOTE]
> SQL 托管实例支持 Azure AD 用户以及 Azure AD 包含的数据库用户的登录名。 以下步骤说明如何为 SQL 托管实例中的 Azure AD guest 用户创建登录名和用户。 还可以通过使用在[Sql 数据库中创建来宾用户和 Azure Synapse](#create-guest-user-in-sql-database-and-azure-synapse)部分中的方法，选择在 sql 托管实例中创建[包含的数据库用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)。

1. 确保已将来宾用户（例如 `user1@gmail.com` ）添加到 Azure AD 中，并为 SQL 托管实例服务器设置了 Azure AD 管理员。 Azure Active Directory 身份验证需要具有 Azure AD 管理员身份验证。

1. 以 Azure AD 管理员身份或具有足够的 SQL 权限的 Azure AD 用户身份连接到 SQL 托管实例服务器，以创建用户，并在数据库上运行以下命令， `master` 为来宾用户创建登录名：

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 现在应会在数据库中为来宾用户创建登录名 `user1@gmail.com` `master` 。

1. 运行以下命令以验证是否已成功创建登录名：

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. 在需要添加来宾用户的数据库上运行以下命令： 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. 现在应会为来宾用户创建数据库用户 `user1@gmail.com` 。

1. 使用 `user1@gmail.com` [SQL SERVER MANAGEMENT STUDIO （SSMS）](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)通过使用身份验证方法**Azure Active Directory-通用**的身份验证方法，以来宾用户身份断开和登录到数据库。 有关详细信息，请参阅[使用多重身份验证 Azure Active Directory 身份验证](authentication-mfa-ssms-overview.md)。

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>将来宾用户设置为 Azure AD 管理员

按照以下步骤将 Azure AD guest 用户设置为 SQL 逻辑服务器的 Azure AD 管理员。

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>设置 SQL 数据库和 Azure Synapse Azure AD 管理员

1. 确保已将来宾用户（例如 `user1@gmail.com` ）添加到 Azure AD 中。

1. 运行以下 PowerShell 命令，将来宾用户添加为 Azure SQL 逻辑服务器的 Azure AD 管理员：

    - `<ResourceGroupName>`将替换为包含 AZURE SQL 逻辑服务器的 Azure 资源组名称。
    - `<ServerName>`将替换为你的 AZURE SQL 逻辑服务器名称。 如果服务器名称为，则将 `myserver.database.windows.net` 替换 `<Server Name>` 为 `myserver` 。
    - `<DisplayNameOfGuestUser>`将替换为你的来宾用户名。

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    你还可以使用 Azure CLI 命令[az sql server ad-admin](https://docs.microsoft.com/cli/azure/sql/server/ad-admin)将来宾用户设置为 Azure sql 逻辑服务器的 Azure AD 管理员。

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>为 SQL 托管实例设置 Azure AD 管理员

1. 确保已将来宾用户（例如 `user1@gmail.com` ）添加到 Azure AD 中。

1. 中转到[Azure 门户](https://portal.azure.com)，并中转到**Azure Active Directory**资源。 在 "**管理**" 下，中转到 "**用户**" 窗格。 选择你的来宾用户，并记录 `Object ID` 。 

1. 运行以下 PowerShell 命令以添加来宾用户作为 SQL 托管实例的 Azure AD 管理员：

    - `<ResourceGroupName>`将替换为包含 SQL 托管实例的 Azure 资源组名称。
    - `<ManagedInstanceName>`将替换为 SQL 托管实例名称。
    - `<DisplayNameOfGuestUser>`将替换为你的来宾用户名。
    - `<AADObjectIDOfGuestUser>`将替换为 `Object ID` 前面收集的。

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    你还可以使用 Azure CLI 命令[az sql mi ad-admin](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin)将来宾用户设置为 sql 托管实例的 Azure AD 管理员。

## <a name="limitations"></a>限制

Azure 门户的限制会阻止选择 Azure AD guest 用户作为 SQL Database 的 Azure AD 管理员、SQL 托管实例和 Azure Synapse。 对于你的 Azure AD 之外的来宾帐户，如* \@ outlook.com*、 * \@ hotmail.com*、 * \@ live.com*或* \@ gmail.com*，AD 管理员选择器将显示这些帐户，但是它们将灰显并且无法选择。 使用上面列出的[PowerShell 或 CLI 命令](#setting-a-guest-user-as-an-azure-ad-admin)设置 Azure AD 管理员。或者，可以将包含来宾用户的 Azure AD 组设置为 SQL 逻辑服务器的 Azure AD 管理员。

在此功能正式发布之前，将为 SQL 数据库和 Azure Synapse 启用此功能。

## <a name="next-steps"></a>后续步骤

- [使用 Azure SQL 配置和管理 Azure AD 身份验证](authentication-aad-configure.md)
- [使用多重 Azure Active Directory 身份验证](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
