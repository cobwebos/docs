---
title: 使用 Azure SQL 的 Azure Active Directory 服务主体
description: Azure AD 应用程序（服务主体）支持在 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 中创建 Azure AD 用户
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: d8268ebf89bed6b67919e77576118343b58edb6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516616"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>使用 Azure SQL 的 Azure Active Directory 服务主体

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

支持以 Azure AD 应用程序（服务主体）身份在 Azure SQL 数据库 (SQL DB) 和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 中创建 Azure Active Directory (Azure AD) 用户的功能当前为**公共预览版**。

> [!NOTE]
> SQL 托管实例已支持此功能。

## <a name="service-principal-azure-ad-applications-support"></a>服务主体（Azure AD 应用程序）支持

本文适用于集成了 Azure AD 并且属于 Azure AD 注册的应用程序。 这些应用程序通常需要对 Azure SQL 进行身份验证和授权访问才能执行各种任务。 **公共预览版**中的此功能现在允许服务主体在 SQL 数据库和 Azure Synapse 中创建 Azure AD 用户。 有一个限制会阻止代表已删除的 Azure AD 应用程序创建 Azure AD 对象这一功能。

使用 Azure 门户或 PowerShell 命令注册 Azure AD 应用程序时，会在 Azure AD 租户中创建两个对象：

- 应用程序对象
- 和一个服务主体对象。

有关 Azure AD 应用程序的详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)和[使用 Azure PowerShell 创建 Azure 服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0)。

SQL 数据库、Azure Synapse 和 SQL 托管实例支持以下 Azure AD 对象：

- Azure AD 用户（托管用户、联合用户和来宾用户）
- Azure AD 组（托管组和联合组）
-  Azure AD 应用程序 

SQL 数据库和 Azure Synapse 现在支持以 Azure AD 应用程序身份运行 T-SQL 命令 `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`。

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>使用服务主体创建 Azure AD 用户的功能

在无需人工交互的 Azure AD 应用程序自动化流程（其中的 Azure AD 对象是在 SQL 数据库和 Azure Synapse 中创建和维护的）中，支持此功能非常有用。 服务主体可以是 SQL 逻辑服务器的 Azure AD 管理员（属于组成员或属于单个用户）。 当以系统管理员身份执行时，应用程序可以在 SQL 数据库和 Azure Synapse 中自动创建 Azure AD 对象，并且不需要任何其他 SQL 权限。 这可以实现完全自动化的数据库用户创建操作。 系统分配的托管标识和用户分配的托管标识也支持此功能。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>使服务主体能够创建 Azure AD 用户

为了能够以 Azure AD 应用程序身份在 SQL 数据库和 Azure Synapse 中创建 Azure AD 对象，需要进行以下设置：

1. 分配服务器标识
    - 对于新的 Azure SQL 逻辑服务器，请执行以下 PowerShell 命令：
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    有关详细信息，请参阅 [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) 命令。

    - 对于现有的 Azure SQL 逻辑服务器，请执行以下命令：
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    有关详细信息，请参阅 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 命令。

    - 若要检查是否为服务器分配了服务器标识，请执行 Get-AzSqlServer 命令。

    > [!NOTE]
    > 也可以使用 CLI 命令分配服务器标识。 有关详细信息，请参阅 [az sql server create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 和 [az sql server update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update)。

2. 向已创建的或分配给服务器的服务器标识授予 Azure AD [**目录读取者**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)权限。
    - 若要授予此权限，请按照以下文章中提供的用于 SQL 托管实例的说明进行操作：[预配 Azure AD 管理员（SQL 托管实例）](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - 授予此权限的 Azure AD 用户必须担任 Azure AD **全局管理员**或**特权角色管理员**角色。

> [!IMPORTANT]
> 必须按上述顺序执行步骤 1 和步骤 2。 首先，创建或分配服务器标识，然后授予[**目录读取者**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)权限。 省略这两个步骤中的一个或同时忽略这两个都会导致以 Azure AD 应用程序身份在 Azure SQL 中创建 Azure AD 对象时发生执行错误。 有关以 Azure AD 应用程序身份创建 Azure AD 用户的分步说明，请参阅[教程：使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)。
>
> 在 **公共预览版**中，你可以将 **目录读取** 者角色分配到 Azure AD 中的组。 然后，组所有者可以将托管标识添加为此组的成员，这样就不需要 **全局管理员** 或 **特权角色管理员** 授予 **目录读者** 角色。 有关此功能的详细信息，请参阅 [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)。

## <a name="troubleshooting-and-limitations-for-public-preview"></a>公共预览版的故障排除和限制

- 当以 Azure AD 应用程序身份在 Azure SQL 中创建 Azure AD 对象时，如果不启用服务器标识并授予**目录读取者**权限，则操作会失败，并出现以下可能的错误。 下面的示例错误针对以下文章中介绍的用于创建 SQL 数据库用户 `myapp` 的 PowerShell 命令执行过程：[教程：使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)。
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - 对于上述错误，请执行相关步骤，[为 Azure SQL 逻辑服务器分配标识](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server)并[为 SQL 逻辑服务器标识分配目录读取者权限](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)。
    > [!NOTE]
    > 上述错误消息会在此功能正式发布之前进行更改，以明确指出要支持 Azure AD 应用程序还需要满足哪些设置要求。
- 仅支持以 SQL 托管实例的 Azure AD 管理员身份使用 CLI 命令以及 [Az.Sql 版本为 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 或更高的 PowerShell 命令设置 Azure AD 应用程序。 有关详细信息，请参阅 [az sql mi ad-admin create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) 和 [Set-AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 命令。 
    - 如果要使用 SQL 托管实例的 Azure 门户来设置 Azure AD 管理员，一种可能的解决方法是创建一个 Azure AD 组。 然后，将服务主体（Azure AD 应用程序）添加到此组，并将此组设置为 SQL 托管实例的 Azure AD 管理员。
    - 支持使用 Azure 门户、[PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse) 和 [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) 命令将服务主体（Azure AD 应用程序）设置为 SQL 数据库和 Azure Synapse 的 Azure AD 管理员。
- 访问在不同租户中创建的 SQL 数据库或 SQL 托管实例时，无法将 Azure AD 应用程序与另一个 Azure AD 租户中的服务主体结合使用。 分配给此应用程序的服务主体必须与 SQL 逻辑服务器或托管实例位于同一租户中。
- 使用 PowerShell 将单个 Azure AD 应用程序设置为 Azure SQL 的 Azure AD 管理员时，需要使用 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 模块或更高版本。 确保已升级到最新模块。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)


