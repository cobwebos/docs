---
title: 使用 Windows VM 系统分配的托管标识访问 Azure AD 图形 API
description: 本教程将指导你完成使用 Windows VM 系统分配的托管标识访问 Azure AD 图形 API 的过程。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed94b7571acb0ced124644dafc59d805d5112e8a
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268560"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>教程：使用 Windows VM 系统分配的托管标识访问 Azure AD 图形 API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Windows 虚拟机 (VM) 系统分配的托管标识访问 Microsoft 图形 API，以检索其组成员身份。 Azure 资源的托管标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。  在本教程中，我们将在 Azure AD 组中查询 VM 标识的成员身份。 例如，授权决策中经常会使用组信息。 在幕后，VM 的托管标识由 Azure AD 中的**服务主体**表示。 在执行组查询之前，请将表示 VM 标识的服务主体添加到 Azure AD 中的某个组。 可以使用 Azure PowerShell、Azure AD PowerShell 或 Azure CLI 执行此操作。

> [!div class="checklist"]
> * 连接到 Azure AD
> * 将 VM 标识添加到 Azure AD 中的某个组 
> * 向 VM 标识授予 Azure AD Graph 的访问权限 
> * 使用 VM 标识获取访问令牌，并使用该令牌调用 Azure AD Graph

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- 向 VM 标识授予 Azure AD Graph 访问权限的帐户需在 Azure AD 中分配有“全局管理员”角色。
- 安装最新的 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)（如果尚未安装）。 

## <a name="connect-to-azure-ad"></a>连接到 Azure AD

需要连接到 Azure AD 才能将 VM 分配到组，以及授予 VM 检索其组成员身份的权限。

```powershell
Connect-AzureAD
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>将 VM 标识添加到 Azure AD 中的某个组

在 Windows VM 上启用系统分配的托管标识时，已在 Azure AD 中创建了一个服务主体。  现在，需将该 VM 添加到组中。  以下示例在 Azure AD 中创建新组，并将 VM 的服务主体添加到该组：

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>授予 VM 对 Azure AD 图形 API 的访问权限

使用 Azure 资源的托管标识，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源进行身份验证。 Microsoft Azure AD 图形 API 支持 Azure AD 身份验证。 此步骤向 VM 标识的服务主体授予 Azure AD Graph 访问权限，以便可以查询组成员身份。 通过**应用程序权限**向服务主体授予 Microsoft 或 Azure AD Graph 的访问权限。 需要授予的应用程序权限类型取决于要在 MS 或 Azure AD Graph 中访问的实体。

在本教程中，我们将使用 ```Directory.Read.All``` 应用程序权限授予 VM 标识查询组成员身份的能力。 若要授予此权限，需要一个在 Azure AD 中分配有“全局管理员”角色的用户帐户。 通常，我们通过在 Azure 门户中访问应用程序的注册并在注册中添加权限，来向应用程序授予权限。 但是，Azure 资源的托管标识不会在 Azure AD 中注册应用程序对象，而只会注册服务主体。 若要注册应用程序权限，应使用 Azure AD PowerShell 命令行工具。 

Azure AD Graph：
- 服务主体 appId（为应用授予权限时使用）：00000002-0000-0000-c000-000000000000
- 资源 ID（从 Azure 资源的托管标识请求访问令牌时使用）： https://graph.windows.net
- 权限范围参考：[Azure AD Graph 权限参考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 为应用程序授予权限

需要安装 Azure AD PowerShell 才能使用此选项。 如果尚未安装，请[下载最新版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)，然后继续。

1. 打开 PowerShell 窗口并连接到 Azure AD：

   ```powershell
   Connect-AzureAD
   ```

2. 运行以下 PowerShell 命令，将 ``Directory.Read.All`` 应用程序权限分配到表示 VM 标识的服务主体。

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   最后一条命令的输出应如下所示，其中返回了分配 ID：
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   如果调用 `New-AzureAdServiceAppRoleAssignment` 失败并出现错误 `bad request, one or more properties are invalid`，则表示应用权限可能已分配到 VM 标识的服务主体。 可使用以下 PowerShell 命令来检查 VM 标识与 Azure AD Graph 之间是否已存在应用程序权限：

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   可使用以下 PowerShell 命令列出已授予 Azure AD Graph 的所有应用权限：

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   可使用以下 PowerShell 命令删除已授予 Azure AD Graph 的 VM 标识的应用权限：

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>使用 VM 的标识获取访问令牌，以调用 Azure AD Graph 

若要使用 VM 的系统分配的托管标识在 Azure AD Graph 中进行身份验证，需从该 VM 发出此请求。

1. 在门户中导航到“虚拟机”，转到自己的 Windows VM，然后在“概述”边栏选项卡中单击“连接”。  
2. 输入创建 Windows VM 时使用的“用户名”和“密码”。
3. 现在已与虚拟机建立远程桌面连接，请在远程会话中打开 PowerShell。  
4. 使用 PowerShell 的 Invoke-WebRequest 向 Azure 资源终结点的本地托管标识发出请求，以获取 Azure AD Graph 的访问令牌。

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   将响应从 JSON 对象转换为 PowerShell 对象。

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   从响应中提取访问令牌。

   ```powershell
   $AccessToken = $content.access_token
   ```

5. 使用 VM 标识的服务主体的对象 ID（可从前面步骤中声明的变量检索此值：``$ManagedIdentitiesServicePrincipal.ObjectId``），可以查询 Azure AD 图形 API 以检索其组成员身份。 请将 <OBJECT ID> 替换为在前一步骤中获取的对象 ID，并将 <ACCESS-TOKEN> 替换为前面获取的访问令牌：

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   响应包含前面步骤中已将 VM 服务主体添加到的组的 `Object ID`。

   响应：

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>后续步骤

在本教程中，我们已学习了如何使用 Windows VM 系统分配的托管标识来访问 Azure AD Graph。  若要详细了解 Azure AD Graph，请参阅：

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
