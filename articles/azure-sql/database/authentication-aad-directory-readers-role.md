---
title: Azure SQL Azure Active Directory 中的目录读者角色
description: Azure AD 应用程序 () 支持 Azure AD 用户在 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 中创建
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: a517a4b14a64be2b9bf12270eef3d08e14d8f3c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556214"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Azure SQL Azure Active Directory 中的目录读者角色

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 本文中的此功能 **公开预览版**。

使用云组 Azure Active Directory (Azure AD) [在 Azure Active Directory (preview) 中管理角色分配 ](../../active-directory/users-groups-roles/roles-groups-concept.md)。 这允许将 Azure AD 角色分配给组。

为 Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse Analytics 启用 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 时，必须将 Azure AD [**目录读取**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 者角色分配给该标识，以允许对 [Azure AD 图形 API](../../active-directory/develop/active-directory-graph-api.md)进行读取访问。 SQL 数据库和 Azure Synapse 的托管标识称为服务器标识。 SQL 托管实例的托管标识称为托管实例标识，并在创建实例时自动分配。 有关将服务器标识分配给 SQL 数据库或 Azure Synapse 的详细信息，请参阅 [启用服务主体以创建 Azure AD 用户](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)。

**目录读者**角色是必需的：

- 为 SQL 托管实例创建 Azure AD 登录名
- 在 Azure SQL 中模拟 Azure AD 用户
- 将使用 Windows 身份验证的 SQL Server 用户迁移到使用 Azure AD authentication 的 SQL 托管实例， (使用 [ALTER USER (transact-sql) ](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) 命令) 
- 更改 SQL 的 Azure AD 管理员托管实例
- 允许) 在 Azure SQL 中创建 Azure AD 用户的[服务主体 (应用程序](authentication-aad-service-principal.md)

## <a name="assigning-the-directory-readers-role"></a>分配目录读者角色

若要将 [**目录读取**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 者角色分配给某个标识，需要具有 [全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 或 [特权角色管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 权限的用户。 经常管理或部署 SQL 数据库、SQL 托管实例或 Azure Synapse 的用户可能无权访问这些高特权角色。 这通常会导致创建未计划的 Azure SQL 资源的用户的复杂性，或者需要在大型组织中经常无法访问的高特权角色成员提供帮助。

对于 SQL 托管实例，必须先将 **目录读取** 者角色分配给托管实例标识，然后才能 [为托管实例设置 Azure AD 管理员](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。 

为逻辑服务器设置 Azure AD 管理员时，SQL 数据库或 Azure Synapse 不需要将 **目录读取** 者角色分配给服务器标识。 但是，若要在 SQL 数据库或 Azure Synapse 中代表 Azure AD 应用程序启用 Azure AD 对象创建，则需要 **目录读取** 者角色。 如果未将该角色分配给 SQL 逻辑服务器标识，则在 Azure SQL 中创建 Azure AD 用户将失败。 有关详细信息，请参阅[使用 Azure SQL 的 Azure Active Directory 服务主体](authentication-aad-service-principal.md)。

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>向 Azure AD 组授予目录读者角色

当前在 **公共预览版**中，你现在可以拥有 [全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 或 [特权角色管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 创建 Azure AD 组，并为该组分配 [**目录读取器**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 权限。 这将允许访问此组成员的 Azure AD 图形 API。 此外 Azure AD，还允许作为此组所有者的用户分配此组的新成员，其中包括 Azure SQL 逻辑服务器的标识。

此解决方案仍要求高特权用户 (全局管理员或特权角色管理员) 创建组并将用户分配为一次性活动，但 Azure AD 组所有者将能够分配其他成员。 这样，以后就不必再涉及到高特权用户在其 Azure AD 租户中配置所有 SQL 数据库、SQL 托管实例或 Azure Synapse 服务器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将目录读取者角色分配到 Azure AD 组和管理角色分配](authentication-aad-directory-readers-role-tutorial.md)
