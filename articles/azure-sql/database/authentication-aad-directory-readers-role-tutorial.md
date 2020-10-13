---
title: 将目录读取者角色分配到 Azure AD 组并管理角色分配
description: 本文介绍如何使用 Azure AD 组启用目录读取者角色，以便使用 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 来管理 Azure AD 角色分配
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d6c447deedbdcc4f2439fc069f368db88b3560b9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278016"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>教程：将目录读取者角色分配到 Azure AD 组并管理角色分配

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 本文中组的目录读取者角色分配为公共预览版 。 

本文介绍如何在 Azure Active Directory (Azure AD) 中创建组，并向该组分配 [目录读取者](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)角色。 通过目录读取者权限，组所有者能够将其他成员添加到组中，如 [Azure SQL 数据库](sql-database-paas-overview.md)的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)、[Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。 这就不需要[全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)或[特权角色管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)来为租户中的每个 Azure SQL 逻辑服务器标识直接分配目录读取者角色。

本教程使用[使用云组来管理 Azure Active Directory 中的角色分配（预览版）](../../active-directory/users-groups-roles/roles-groups-concept.md)中引入的功能。 

如需详细了解将目录读取者角色分配给 Azure SQL 的 Azure AD 组的优势，请参阅 [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)。

## <a name="prerequisites"></a>先决条件

- Azure AD 实例。 有关详细信息，请参阅[使用 Azure SQL 配置和管理 Azure AD 身份验证](authentication-aad-configure.md)。
- SQL 数据库、SQL 托管实例或 Azure Synapse。

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>使用 Azure 门户的目录读取者角色分配

### <a name="create-a-new-group-and-assign-owners-and-role"></a>创建新组并分配所有者和角色

1. 此初始设置需要具有[全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)或[特权角色管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)权限的用户。
1. 让特权用户登录到 [Azure 门户](https://portal.azure.com)。
1. 转到 Azure Active Directory 资源。 在“托管”下，转到“组” 。 选择“新建组”以创建一个新组。
1. 选择“安全”作为组类型，并填充其余字段。 确保可以将设置“Azure AD 角色可以分配给组（预览）”切换为“是” 。 然后，将 Azure AD 目录读取者角色分配给组。
1. 将 Azure AD 用户作为所有者分配给创建的组。 组所有者可以是未分配任何 Azure AD 管理角色的常规 AD 用户。 所有者应该是管理 SQL 数据库、SQL 托管实例或 Azure Synapse 的用户。

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. 选择“创建”

### <a name="checking-the-group-that-was-created"></a>检查创建的组

> [!NOTE]
> 确保“组类型”为“安全” 。 Azure SQL 不支持 Microsoft 365 组。

若要检查和管理已创建的组，请返回到 Azure 门户中的“组”窗格，并搜索组名称。 选择组后，可以在“管理”设置的“所有者”和“成员”菜单下添加其他所有者和成员  。 还可查看组的“已分配角色”。

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="aad-new-group":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>向组添加 Azure SQL 托管标识

> [!NOTE]
> 虽然我们在此示例中使用 SQL 托管实例，但可以对 SQL 数据库或 Azure Synapse 应用类似的步骤，以获得相同的结果。

在后续步骤中，不再需要全局管理员或特权角色管理员用户。

1. 以管理 SQL 托管实例的用户身份登录 Azure 门户，并且是之前创建的组的所有者。

1. 在 Azure 门户中查找 SQL 托管实例资源的名称。

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="aad-new-group":::

   在创建 SQL 托管实例的过程中，系统会为你的实例创建 Azure 标识。 创建的标识与 SQL 托管实例名称的前缀同名。 通过执行以下步骤，可查找作为 Azure AD 应用程序创建的 SQL 托管实例标识的服务主体：

    - 转到 Azure Active Directory 资源。 在“管理”设置下，选择“企业应用程序” 。 对象 ID 是实例的标识。
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="aad-new-group":::

1. 转到 Azure Active Directory 资源。 在“托管”下，转到“组” 。 选择所创建的组。 在组的“托管”设置下，选择“成员” 。 选择“添加成员”，并通过搜索上面找到的名称，将 SQL 托管实例服务主体添加为组的成员。

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="aad-new-group":::

> [!NOTE]
> 需要花费几分钟时间才能通过 Azure 系统传播服务主体权限，并允许访问 Azure AD 图形 API。 在为 SQL 托管实例预配 Azure AD 管理员之前，可能需要等待几分钟。

### <a name="remarks"></a>注解

对于 SQL 数据库和 Azure Synapse，可以在创建 Azure SQL 逻辑服务器期间或创建服务器之后创建服务器标识。 有关如何在 SQL 数据库或 Azure Synapse 中创建或设置服务器标识的详细信息，请参阅[启用服务主体以创建 Azure AD 用户](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)。

对于 SQL 托管实例，必须先将目录读取者角色分配给托管实例标识，然后才能[为托管实例设置 Azure AD 管理员](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。

为逻辑服务器设置 Azure AD 管理员时，SQL 数据库或 Azure Synapse 不需要向服务器标识分配目录读取者角色。 但是，若要在 SQL 数据库或 Azure Synapse 中代表 Azure AD 应用程序启用 Azure AD 对象创建，则需要目录读取者角色。 如果未将该角色分配给 SQL 逻辑服务器标识，则在 Azure SQL 中创建 Azure AD 用户将失败。 有关详细信息，请参阅[使用 Azure SQL 的 Azure Active Directory 服务主体](authentication-aad-service-principal.md)。

## <a name="directory-readers-role-assignment-using-powershell"></a>使用 PowerShell 的目录读取者角色分配

> [!IMPORTANT]
> [全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)或[特权角色管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)需要运行这些初始步骤。 除 PowerShell 外，Azure AD 还提供 Microsoft 图形 API 以[在 Azure AD 中创建可分配角色的组](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api)。

1. 使用以下命令下载 Azure AD 预览版 PowerShell 模块。 可能需要以管理员身份运行 PowerShell。

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. 连接到 Azure AD 租户。

    ```powershell
    Connect-AzureAD
    ```

1. 创建安全组以分配目录读取者角色。

    - `DirectoryReaderGroup`、`Directory Reader Group` 和 `DirRead` 可根据你的喜好进行更改。

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. 将目录读取者角色分配给组。

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. 将所有者分配给组。

    - 将 `<username>` 替换为你想要其拥有该组的用户。 通过重复这些步骤来添加多个所有者。

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    使用以下命令来检查组的所有者：

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    还可在 [Azure 门户](https://portal.azure.com)中验证组的所有者。 执行[检查创建的组](#checking-the-group-that-was-created)中的步骤。

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>将服务主体分配为组的成员

在后续步骤中，不再需要全局管理员或特权角色管理员用户。

1. 使用也可管理 Azure SQL 资源的组所有者，运行以下命令以连接到 Azure AD。

    ```powershell
    Connect-AzureAD
    ```

1. 将服务主体分配为所创建组的成员。

    - 将 `<ServerName>` 替换为 Azure SQL 逻辑服务器名称或托管实例名称。 有关详细信息，请参阅[将 Azure SQL 服务标识添加到组](#add-azure-sql-managed-identity-to-the-group)部分

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    以下命令将返回服务主体对象 ID，指示已将其添加到组：

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>后续步骤

- [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)
- [教程：使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)
- [使用 Azure SQL 配置和管理 Azure AD 身份验证](authentication-aad-configure.md)