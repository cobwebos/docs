---
title: 使用服务主体创建 Azure AD 用户
description: 本教程介绍如何在 Azure SQL 数据库和 Azure Synapse Analytics 中使用 Azure AD 应用程序（服务主体）创建 Azure AD 用户
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: 61cb5384fd4d935ef4038c18b391b5da5fbc96b1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516684"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>教程：使用 Azure AD 应用程序创建 Azure AD 用户

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> 本文目前以公共预览版提供。 有关详细信息，请参阅[使用 Azure SQL 的 Azure Active Directory 服务主体](authentication-aad-service-principal.md)。 本文将使用 Azure SQL 数据库来演示必要的教程步骤，但同样适用于 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。

本文将指导你完成使用 Azure 服务主体（Azure AD 应用程序）在 Azure SQL 数据库中创建 Azure AD 用户的过程。 此功能已存在于 Azure SQL 托管实例中，但现在在 Azure SQL 数据库和 Azure Synapse Analytics 中引入。 若要支持此方案，必须生成 Azure AD 标识并将其分配给 Azure SQL 逻辑服务器。

有关 Azure SQL 的 Azure AD 身份验证的详细信息，请参阅[使用 Azure Active Directory 身份验证](authentication-aad-overview.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 将标识分配给 Azure SQL 逻辑服务器
> - 将目录读取者权限分配给 SQL 逻辑服务器标识
> - 在 Azure AD 中创建服务主体（Azure AD 应用程序）
> - 在 Azure SQL 数据库中创建服务主体用户
> - 使用 Azure AD 服务主体用户在 SQL 数据库中创建其他 Azure AD 用户

## <a name="prerequisites"></a>先决条件

- 现有 [Azure SQL 数据库](single-database-create-quickstart.md)或 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 部署。 在本教程中，我们假设你有一个正常运行的 SQL 数据库。
- 可以访问现有 Azure Active Directory。
- 使用 PowerShell 将单个 Azure AD 应用程序设置为 Azure SQL 的 Azure AD 管理员时，需要使用 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 模块或更高版本。 确保已升级到最新模块。

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>将标识分配给 Azure SQL 逻辑服务器

1. 连接到 Azure Active Directory。 需要找到你的租户 ID。 可以通过转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Active Directory 资源来找到该 ID。 在“概述”窗格中，应会看到“租户 ID”。 运行以下 PowerShell 命令：

    - 使用租户 ID 替换 `<TenantId>`。

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    记录 `TenantId` 以便将来在本教程中使用。

1. 生成 Azure AD 标识并将其分配给 Azure SQL 逻辑服务器。 执行以下 PowerShell 命令：

    - 将 `<resource group>` 和 `<server name>` 替换为你的资源。 如果服务器名称为 `myserver.database.windows.net`，请将 `<server name>` 替换为 `myserver`。

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    有关详细信息，请参阅 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 命令。

    > [!IMPORTANT]
    > 如果已为 Azure SQL 逻辑服务器设置了 Azure AD 标识，则必须向该标识授予[目录读取者](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)权限。 我们将在下一部分中指导你完成此步骤。 请勿跳过此步骤，因为 Azure AD 身份验证将停止工作。

    - 如果过去已将带有参数 `AssignIdentity` 的 [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) 命令用于创建新的 SQL Server，则之后需要将 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 命令作为单独的命令执行，以便在 Azure 结构中启用此属性。

1. 检查是否已成功分配服务器标识。 执行以下 PowerShell 命令：

    - 将 `<resource group>` 和 `<server name>` 替换为你的资源。 如果服务器名称为 `myserver.database.windows.net`，请将 `<server name>` 替换为 `myserver`。
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    你的输出应显示 `PrincipalId`、`Type` 和 `TenantId`。 分配的标识是 `PrincipalId`。

1. 还可以通过转到 [Azure 门户](https://portal.azure.com)检查标识。

    - 在“Azure Active Directory”资源下，转到“企业应用程序”。 键入 SQL 逻辑服务器的名称。 你将看到其中包含附加到资源的对象 ID。
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>将目录读取者权限分配给 SQL 逻辑服务器标识

若要允许 Azure AD 分配的标识适用于 Azure SQL，必须向该服务器标识授予 Azure AD `Directory Readers` 权限。

若要授予此所需权限，请运行以下脚本。

> [!NOTE] 
> 此脚本必须由 Azure AD `Global Administrator` 或 `Privileged Roles Administrator` 执行。
>
> 在“公共预览”中，可以在 Azure AD 中将 `Directory Readers` 角色分配给组。 然后，组所有者可以将托管标识添加为此组的成员，这样就不需要 `Global Administrator` 或 `Privileged Roles Administrator` 来授予 `Directory Readers` 角色。 有关此功能的详细信息，请参阅 [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)。

- 将 `<TenantId>` 替换为之前收集的 `TenantId`。
- 将 `<server name>` 替换为你的 SQL 逻辑服务器名称。 如果服务器名称为 `myserver.database.windows.net`，请将 `<server name>` 替换为 `myserver`。

```powershell
# This script grants Azure “Directory Readers” permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or “Privileged Roles Administrator” type of user.
# To check if the “Directory Readers" permission was granted, execute this script again

Import-Module AzureAd
connect-azuread -TenantId "<TenantId>"     #Enter your actual TenantId
 $AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null)
{
    Write-Output "Error: No Service Principals with name '$    ($AssignIdentityName)', make sure that AssignIdentityName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1))
{
    Write-Output "Error: More than one service principal with name pattern '$    ($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}
 
if ($selDirReader -eq $null)
{
    # Add principal to readers role
    Write-Output "Adding service principal '$($msName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to     'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else
{
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> 上述脚本的输出将指示是否向该标识授予了目录读取者权限。 如果不确定是否已授予权限，可以重新运行该脚本。

有关如何为 SQL 托管实例设置目录读取者权限的类似方法，请参阅[预配 Azure AD 管理员（SQL 托管实例）](authentication-aad-configure.md#powershell)。

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>在 Azure AD 中创建服务主体（Azure AD 应用程序）

1. 请按照此处的指南[注册应用并设置权限](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions)。

    请确保添加应用程序权限以及委托的权限。

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="aad-apps":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api-permissions":::

2. 还需要创建用于登录的客户端密码。 请按照此处的指南[上传证书或创建用于登录的机密](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in)。

3. 记录应用程序注册中的以下信息。 应在“概述”窗格中提供该信息：
    - **应用程序 ID**
    - 租户 ID - 这应与前面的相同

在本教程中，我们将使用 AppSP 作为主服务主体，使用 myapp 作为将在 Azure SQL 中通过 AppSP 创建的第二个服务主体用户。 需要创建两个应用程序：AppSP 和 myapp。

有关如何创建 Azure AD 应用程序的详细信息，请参阅文章[如何使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>在 Azure SQL 数据库中创建服务主体用户

在 Azure AD 中创建服务主体后，在 SQL 数据库中创建该用户。 需要使用具有在数据库中创建用户的权限的有效登录名连接到 SQL 数据库。

1. 使用以下 T-SQL 命令在 SQL 数据库中创建用户 AppSP：

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. 向 AppSP 授予 `db_owner` 权限，这允许用户在数据库中创建其他 Azure AD 用户。

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    有关详细信息，请参阅 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    或者，可以授予 `ALTER ANY USER` 权限，而不是授予 `db_owner` 角色。 这将允许服务主体添加其他 Azure AD 用户。

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > 将 AppSP 设置为服务器的 Azure AD 管理员时，不需要上述设置。 若要将服务主体设置为 SQL 逻辑服务器的 AD 管理员，可以使用 Azure 门户、PowerShell 或 Azure CLI 命令。 有关详细信息，请参阅[预配 Azure AD 管理员（SQL 数据库）](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)。

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>使用 Azure AD 服务主体在 SQL 数据库中创建 Azure AD 用户

> [!IMPORTANT]
> 用于登录到 SQL 数据库的服务主体必须具有客户端密码。 如果没有，请执行[在 Azure AD 中创建服务主体（Azure AD 应用程序）](#create-a-service-principal-an-azure-ad-application-in-azure-ad)的步骤 2。 需要在下面的脚本中将此客户端密码添加为输入参数。

1. 使用以下脚本来使用服务主体 AppSP 创建 Azure AD 服务主体用户 myapp。

    - 将 `<TenantId>` 替换为之前收集的 `TenantId`。
    - 将 `<ClientId>` 替换为之前收集的 `ClientId`。
    - 将 `<ClientSecret>` 替换为之前创建的客户端密码。
    - 将 `<server name>` 替换为你的 SQL 逻辑服务器名称。 如果服务器名称为 `myserver.database.windows.net`，请将 `<server name>` 替换为 `myserver`。
    - 将 `<database name>` 替换为你的 SQL 数据库名称。

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    或者，可以使用博客[对 SQL DB 进行 Azure AD 服务主体身份验证 - 代码示例](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)中的代码示例。 修改脚本以执行 DDL 语句 `CREATE USER [myapp] FROM EXTERNAL PROVIDER`。 相同的脚本可用于在 SQL 数据库中创建常规 Azure AD 用户组。

    > [!NOTE]
    > 如果需要安装模块 AzureRM.profile，则需要以管理员身份打开 PowerShell。 可以使用以下命令自动安装最新的 AzureRM.profile 版本，并为上述脚本设置 `$adalpath`：
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. 通过执行以下命令，检查用户 myapp 是否存在于数据库中：

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    应看到如下类似输出：

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure SQL 的 Azure Active Directory 服务主体](authentication-aad-service-principal.md)
- [什么是 Azure 资源的托管标识？](../../active-directory/managed-identities-azure-resources/overview.md)
- [如何使用应用服务和 Azure Functions 的托管标识](../../app-service/overview-managed-identity.md)
- [对 SQL DB 进行Azure AD 服务主体身份验证 - 代码示例](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Azure Active Directory 中的应用程序对象和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)
- [使用 Azure PowerShell 创建 Azure 服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
- [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)