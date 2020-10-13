---
title: 创建 Azure AD 来宾用户
description: 如何在 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 中创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员，而无需使用 Azure AD 组
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: aa74489a962708a1d3d5e6835f684e5cb8fc548b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444340"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 本文目前以公共预览版提供。

Azure Active Directory (Azure AD) 中的来宾用户是从其他 Azure Active Directory 或其外部导入当前 Azure AD 的用户。    例如，来宾用户可以包含来自其他 Azure Active Directory 的用户，或者来自 \@outlook.com、\@hotmail.com、\@live.com或 \@gmail.com 等帐户的用户。 本文演示如何创建 Azure AD 来宾用户，并将其设置为 Azure SQL 逻辑服务器的 Azure AD 管理员，而无需让该来宾用户成为 Azure AD 中组的一部分。

## <a name="feature-description"></a>功能描述

此功能摆脱了当前限制，即当来宾用户属于 Azure AD 中创建的组的成员时，仅允许他们连接到 Azure SQL 数据库、SQL 托管实例或 Azure Synapse Analytics。 在给定数据库中，需要使用 [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 语句手动将组映射到用户。 为包含来宾用户的 Azure AD 组创建数据库用户后，来宾用户就可以使用 Azure Active Directory 通过 MFA 身份验证登录到数据库。 作为此公共预览版的一部分，可以创建来宾用户并将其直接连接到 SQL 数据库、SQL 托管实例或 Azure Synapse，而无需先将其添加到 Azure AD 组，再为该 Azure AD 组创建数据库用户。

作为此功能的一部分，还可以将 Azure AD 来宾用户直接设置为 Azure SQL 逻辑服务器的 AD 管理员。 现有功能（来宾用户可以成为 Azure AD 组一部分，然后可将改组设为 Azure SQL 逻辑服务器的 AD 管理员）不受影响。 作为 Azure AD 组一部分的数据库中来宾用户也不受此更改的影响。

若要详细了解使用 Azure AD 组时对来宾用户的现有支持，请参阅[使用 Azure Active Directory 多重身份验证](authentication-mfa-ssms-overview.md)。

## <a name="prerequisite"></a>先决条件

- 使用 PowerShell 将来宾用户设为 Azure SQL 逻辑服务器的 Azure AD 管理员时，需要使用 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 模块或更高版本。

## <a name="create-database-user-for-azure-ad-guest-user"></a>创建 Azure AD 来宾用户的数据库用户 

按照以下步骤使用 Azure AD 来宾用户创建数据库用户。

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>在 SQL 数据库和 Azure Synapse 中创建来宾用户

1. 确保已将来宾用户（例如 `user1@gmail.com`）添加到 Azure AD，并为数据库服务器设置了 Azure AD 管理员。 Azure Active Directory 身份验证需要用到 Azure AD 管理员。

1. 以 Azure AD 管理员身份或具有足够 SQL 权限的 Azure AD 用户身份连接到 SQL 数据库以创建用户，并在需要添加来宾用户的数据库上运行以下命令：

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 现在应为来宾用户 `user1@gmail.com` 创建了数据库用户。

1. 运行以下命令，验证是否已成功创建数据库用户：

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. 断开连接，并使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 通过身份验证方法“Azure Active Directory - 通用且具有 MFA”以来宾用户 `user1@gmail.com` 的身份登录到数据库。 有关详细信息，请参阅[使用 Azure Active Directory 多重身份验证](authentication-mfa-ssms-overview.md)。

### <a name="create-guest-user-in-sql-managed-instance"></a>在 SQL 托管实例中创建来宾用户

> [!NOTE]
> SQL 托管实例支持 Azure AD 用户以及 Azure AD 包含的数据库用户的登录名。 以下步骤说明如何在 SQL 托管实例中为 Azure AD 来宾用户创建登录名和用户。 还可以通过使用[在 SQL 数据库和 Azure Synapse 中创建来宾用户](#create-guest-user-in-sql-database-and-azure-synapse)部分介绍的方法，选择在 SQL 托管实例中创建[包含的数据库用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)。

1. 确保已将来宾用户（例如 `user1@gmail.com`）添加到 Azure AD，并为 SQL 托管实例服务器设置了 Azure AD 管理员。 Azure Active Directory 身份验证需要用到 Azure AD 管理员。

1. 以 Azure AD 管理员身份或具有足够 SQL 权限的 Azure AD 用户身份连接到 SQL 托管实例服务器以创建用户，并在 `master` 数据库上运行以下命令来创建来宾用户的登录名：

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 现在应为 `master` 数据库中的来宾用户 `user1@gmail.com` 创建了登录名。

1. 运行以下命令，验证是否已成功创建登录名：

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. 在需要添加来宾用户的数据库上运行以下命令： 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. 现在应为来宾用户 `user1@gmail.com` 创建了数据库用户。

1. 断开连接，并使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 通过身份验证方法“Azure Active Directory - 通用且具有 MFA”以来宾用户 `user1@gmail.com` 的身份登录到数据库。 有关详细信息，请参阅[使用 Azure Active Directory 多重身份验证](authentication-mfa-ssms-overview.md)。

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>将来宾用户设为 Azure AD 管理员

按照以下步骤将 Azure AD 来宾用户设为 SQL 逻辑服务器的 Azure AD 管理员。

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>设置 SQL 数据库和 Azure Synapse 的 Azure AD 管理员

1. 确保已将来宾用户（例如 `user1@gmail.com`）添加到 Azure AD。

1. 运行以下 PowerShell 命令，将来宾用户添加为 Azure SQL 逻辑服务器的 Azure AD 管理员：

    - 将 `<ResourceGroupName>` 替换为包含 Azure SQL 逻辑服务器的 Azure 资源组名称。
    - 将 `<ServerName>` 替换为 Azure SQL 逻辑服务器名称。 如果服务器名称为 `myserver.database.windows.net`，请将 `<Server Name>` 替换为 `myserver`。
    - 请将 `<DisplayNameOfGuestUser>` 替换为来宾用户名。

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    还可以使用 Azure CLI 命令 [az sql server ad-admin](https://docs.microsoft.com/cli/azure/sql/server/ad-admin) 将来宾用户设为 Azure SQL 逻辑服务器的 Azure AD 管理员。

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>设置 SQL 托管实例的 Azure AD 管理员

1. 确保已将来宾用户（例如 `user1@gmail.com`）添加到 Azure AD。

1. 转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Active Directory 资源。 在“管理”下转到“用户”窗格 。 选择来宾用户，并记录 `Object ID`。 

1. 运行以下 PowerShell 命令，将来宾用户添加为 SQL 托管实例的 Azure AD 管理员：

    - 将 `<ResourceGroupName>` 替换为包含 SQL 托管实例的 Azure 资源组名称。
    - 将 `<ManagedInstanceName>` 替换为 SQL 托管实例名称。
    - 请将 `<DisplayNameOfGuestUser>` 替换为来宾用户名。
    - 将 `<AADObjectIDOfGuestUser>` 替换为之前收集的 `Object ID`。

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    还可以使用 Azure CLI 命令 [az sql mi ad-admin](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin) 将来宾用户设为 SQL 托管实例的 Azure AD 管理员。

## <a name="limitations"></a>限制

Azure 门户一个限制会阻止将 Azure AD 来宾用户选为 SQL 托管实例的 Azure AD 管理员。 对于 Azure AD 外部的来宾帐户（如 \@outlook.com、\@hotmail.com、\@live.com 或 \@gmail.com），AD 管理员选择器会显示这些帐户，但它们是灰显的且不能选择它们   。 使用上面列出的 [PowerShell 或 CLI 命令](#setting-a-guest-user-as-an-azure-ad-admin)设置 Azure AD 管理员。或者，可以将包含来宾用户的 Azure AD 组设为 SQL 托管实例的 Azure AD 管理员。

此功能正式发布之前，将为 SQL 托管实例启用此功能。

## <a name="next-steps"></a>后续步骤

- [使用 Azure SQL 配置和管理 Azure AD 身份验证](authentication-aad-configure.md)
- [使用多重 Azure Active Directory 身份验证](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
