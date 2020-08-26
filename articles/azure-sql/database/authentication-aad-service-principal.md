---
title: 使用 Azure SQL 的 Azure Active Directory 服务主体
description: Azure AD 应用程序 () 支持 Azure AD 用户在 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 中创建
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: d8268ebf89bed6b67919e77576118343b58edb6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516616"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>使用 Azure SQL 的 Azure Active Directory 服务主体

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

在 Azure SQL 数据库中支持 Azure Active Directory (Azure AD) 用户在 Azure SQL 数据库中创建 () Azure AD 的 (应用程序) 的应用 [程序，](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 当前为 **公共预览版**。

> [!NOTE]
> SQL 托管实例已支持此功能。

## <a name="service-principal-azure-ad-applications-support"></a>服务主体 (Azure AD 应用程序) 支持

本文适用于与 Azure AD 集成并且属于 Azure AD 注册的应用程序。 这些应用程序通常需要身份验证和授权才能访问 Azure SQL 来执行各种任务。 **公共预览版**中的此功能现在允许服务主体在 SQL 数据库和 Azure Synapse 中创建 Azure AD 用户。 阻止 Azure AD 已删除应用程序创建 Azure AD 对象的限制。

使用 Azure 门户或 PowerShell 命令注册 Azure AD 应用程序时，会在 Azure AD 租户中创建两个对象：

- 应用程序对象
- 和一个服务主体对象。

有关 Azure AD 应用程序的详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md) 和 [使用 Azure PowerShell 创建 Azure 服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0)。

SQL 数据库、Azure Synapse 和 SQL 托管实例支持以下 Azure AD 对象：

- Azure AD 用户 (管理、联合和来宾) 
- Azure AD 组 (管理和联合) 
-  Azure AD 应用程序 

`CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`SQL 数据库和 Azure Synapse 现在支持代表 Azure AD 应用程序的 t-sql 命令。

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>使用服务主体 Azure AD 用户创建功能

如果在 SQL 数据库中创建和维护 Azure AD 对象的 Azure AD 应用程序自动化进程，而无需人工交互，则支持此功能很有用。 服务主体可以是 SQL 逻辑服务器 Azure AD 管理员，作为组或单个用户的一部分。 当以系统管理员身份执行时，应用程序可以在 SQL 数据库和 Azure Synapse 中自动执行 Azure AD 对象创建，并且不需要任何其他 SQL 特权。 这允许创建数据库用户的完全自动化。 系统分配的托管标识和用户分配的托管标识也支持此功能。 有关详细信息，请参阅 [Azure 资源的托管标识是什么？](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>启用服务主体以创建 Azure AD 用户

若要在 SQL 数据库和 Azure Synapse 中代表 Azure AD 应用程序启用 Azure AD 对象创建，需要以下设置：

1. 分配服务器标识
    - 对于新的 Azure SQL 逻辑服务器，请执行以下 PowerShell 命令：
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    有关详细信息，请参阅 [AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) 命令。

    - 对于现有的 Azure SQL 逻辑服务器，执行以下命令：
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    有关详细信息，请参阅 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 命令。

    - 若要检查服务器标识是否分配给服务器，请执行 AzSqlServer 命令。

    > [!NOTE]
    > 也可以使用 CLI 命令分配服务器标识。 有关详细信息，请参阅 [az sql server create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 和 [az sql server update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update)。

2. 向 Azure AD [**Directory 读者**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 授予对服务器标识创建或分配给服务器的权限。
    - 若要授予此权限，请按照以下文章中提供的用于 SQL 托管实例的说明进行操作： [预配 Azure AD 管理员 (SQL 托管实例) ](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - 授予此权限的 Azure AD 用户必须是 "Azure AD **全局管理员** " 或 " **特权角色管理员** " 角色的一部分。

> [!IMPORTANT]
> 必须按上述顺序执行步骤1和步骤2。 首先，创建或分配服务器标识，然后授予 [**目录读取器**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 权限。 省略这些步骤中的一个或两个都将导致在 Azure SQL 中代表 Azure AD 应用程序创建 Azure AD 对象时执行错误。 有关代表 Azure AD 应用程序创建 Azure AD 用户的分步说明，请参阅 [教程：使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)。
>
> 在 **公共预览版**中，你可以将 **目录读取** 者角色分配到 Azure AD 中的组。 然后，组所有者可以将托管标识添加为此组的成员，这样就不需要 **全局管理员** 或 **特权角色管理员** 授予 **目录读者** 角色。 有关此功能的详细信息，请参阅 [AZURE SQL Azure Active Directory 中的目录读者角色](authentication-aad-directory-readers-role.md)。

## <a name="troubleshooting-and-limitations-for-public-preview"></a>公共预览版的故障排除和限制

- 当代表 Azure AD 应用程序在 Azure SQL 中创建 Azure AD 对象时，如果不启用服务器标识并授予 **目录读取器** 权限，则操作将失败，并出现以下可能的错误。 下面的示例错误用于在教程中创建 SQL 数据库用户 `myapp` [：创建使用 Azure AD 应用程序 Azure AD 用户](authentication-aad-service-principal-tutorial.md)。
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - 对于上述错误，请按照步骤将 [标识分配给 AZURE sql 逻辑服务器](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) ，并 [将目录读取器权限分配给 SQL 逻辑服务器标识](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)。
    > [!NOTE]
    > 上述错误消息将在此功能正式发布之前进行更改，以明确识别缺少的 Azure AD 应用程序支持的设置要求。
- 仅支持使用 CLI 命令和包含 [Az 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 或更高版本的 PowerShell 命令将 Azure AD 应用程序设置为 SQL 托管实例的 Azure AD 管理员。 有关详细信息，请参阅 [az sql mi ad-admin create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) 和 [AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 命令。 
    - 如果要使用 SQL 托管实例 Azure 门户来设置 Azure AD 管理员，一种可能的解决方法是创建 Azure AD 组。 然后，将服务主体 (Azure AD 应用程序) 添加到此组中，并将此组设置为 SQL 托管实例的 Azure AD 管理员。
    - 使用 Azure 门户、 [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)和 [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) 命令，将服务主体 (Azure AD 应用程序) 设置为 SQL 数据库 Azure AD 管理员和 Azure Synapse。
- 当访问在不同租户中创建的 SQL 数据库或 SQL 托管实例时，将 Azure AD 应用程序与另一个 Azure AD 租户中的服务主体结合使用时，将会失败。 分配到此应用程序的服务主体必须与 SQL 逻辑服务器或托管实例位于同一租户中。
- 使用 PowerShell 将单个 Azure AD 应用程序设置为 Azure SQL 的 Azure AD 管理员时，需要使用 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 模块或更高版本。 确保已升级到最新模块。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)


