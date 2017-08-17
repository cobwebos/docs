---
title: "通过 PowerShell 为 Azure 应用创建标识 | Microsoft Docs"
description: "介绍如何使用 Azure PowerShell 创建 Azure Active Directory 应用程序和服务主体，并通过基于角色的访问控制向其授予资源访问权限。 它演示如何使用密码或证书对应用程序进行身份验证。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 36b4cd0674e0f9cec6fb2b00e809c71ee38a80c0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>使用 Azure PowerShell 创建服务主体来访问资源

当有应用或脚本需访问资源时，可以为应用设置一个标识，然后使用其自己的凭据对应用进行身份验证。 此标识称为服务主体。 使用此方法能够：

* 将权限分配给应用标识，这些权限不同于你自己的权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 执行无人参与的脚本时，使用证书进行身份验证。

本主题介绍如何通过 [Azure PowerShell](/powershell/azure/overview) 为应用程序进行一切所需设置，使之能够使用自己的凭据和标识运行。

## <a name="required-permissions"></a>所需的权限
若要完成本主题，必须在 Azure Active Directory 和 Azure 订阅中均具有足够的权限。 具体而言，必须能够在 Azure Active Directory 中创建应用并向角色分配服务主体。 

检查帐户是否有足够权限的最简方法是使用门户。 请参阅[检查所需的权限](resource-group-create-service-principal-portal.md#required-permissions)。

现在，继续完成进行身份验证的部分：

* [password](#create-service-principal-with-password)
* [自签名证书](#create-service-principal-with-self-signed-certificate)
* [证书颁发机构的证书](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>PowerShell 命令

若要设置服务主体，请使用：

| 命令 | 说明 |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | 创建 Azure Active Directory 服务主体 |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | 在指定范围内将指定的 RBAC 角色分配给指定的主体。 |


## <a name="create-service-principal-with-password"></a>使用密码创建服务主体

若要使用订阅的参与者角色创建服务主体，请使用： 

```powershell
Login-AzureRmAccount
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password "{provide-password}"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

该示例休眠 20 秒，让新的服务主体有时间传遍 Azure Active Directory。 如果脚本等待时长不足，将显示错误，称“PrincipalNotFound: 主体 {id} 不存在于目录中”。

使用以下脚本可以指定默认订阅以外的范围，并在出错时重试角色分配：

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

关于该脚本的几个注意事项：

* 若要授予对默认订阅的标识访问权限，不必提供 ResourceGroup 或 SubscriptionId 参数。
* 仅当希望将角色分配范围限定为某个资源组时指定 ResourceGroup 参数。
*  在此示例中，将服务主体添加到“参与者”角色。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。
* 该脚本休眠 15 秒，让新的服务主体有时间传遍 Azure Active Directory。 如果脚本等待时长不足，将显示错误，称“PrincipalNotFound: 主体 {id} 不存在于目录中”。
* 如果需要授予对更多订阅或资源组的服务主体访问权限，则再次针对不同的作用域运行 `New-AzureRMRoleAssignment` cmdlet。


### <a name="provide-credentials-through-powershell"></a>通过 PowerShell 提供凭据
现在，需要以应用程序方式登录以执行相应操作。 对于用户名，请使用为应用程序创建的 `ApplicationId`。 对于密码，请使用你在创建帐户时指定的密码。 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-id}
```

租户 ID 不敏感，因此可以直接将其嵌入脚本中。 如果需要检索租户 ID，请使用：

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>使用自签名证书创建服务主体

若要使用自签名证书和订阅的参与者角色创建服务主体，请使用： 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

该示例休眠 20 秒，让新的服务主体有时间传遍 Azure Active Directory。 如果脚本等待时长不足，将显示错误，称“PrincipalNotFound: 主体 {id} 不存在于目录中”。

使用以下脚本可以指定默认订阅以外的范围，并在出错时重试角色分配。 必须在 Windows 10 或 Windows Server 2016 上使用 Azure PowerShell 2.0。

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

关于该脚本的几个注意事项：

* 若要授予对默认订阅的标识访问权限，不必提供 ResourceGroup 或 SubscriptionId 参数。
* 仅当希望将角色分配范围限定为某个资源组时指定 ResourceGroup 参数。
* 在此示例中，将服务主体添加到“参与者”角色。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。
* 该脚本休眠 15 秒，让新的服务主体有时间传遍 Azure Active Directory。 如果脚本等待时长不足，将显示错误，称“PrincipalNotFound: 主体 {id} 不存在于目录中”。
* 如果需要授予对更多订阅或资源组的服务主体访问权限，则再次针对不同的作用域运行 `New-AzureRMRoleAssignment` cmdlet。

如果未使用 **Windows 10 或 Windows Server 2016 Technical Preview**，需要从 Microsoft 脚本中心下载[自签名证书生成器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)。 解压其内容，并导入所需的 cmdlet。

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
在脚本中，替换以下两行以生成证书。
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>通过自动执行的 PowerShell 脚本提供证书
以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure Active Directory 的实例。 如果只有一个订阅，可以使用：

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

应用程序 ID 和租户 ID 不敏感，因此可以直接将它们嵌入脚本中。 如果需要检索租户 ID，请使用：

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

如果需要检索应用程序 ID，请使用：

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>使用来自证书颁发机构的证书创建服务主体
若要使用证书颁发机构颁发的证书创建服务主体，请使用以下脚本：

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId

 $KeyId = (New-Guid).Guid
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
 $KeyCredential.StartDate = $PFXCert.NotBefore
 $KeyCredential.EndDate= $PFXCert.NotAfter
 $KeyCredential.KeyId = $KeyId
 $KeyCredential.CertValue = $KeyValue

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -KeyCredentials $keyCredential
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

关于该脚本的几个注意事项：

* 访问范围限定于订阅。
* 在此示例中，将服务主体添加到“参与者”角色。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。
* 该脚本休眠 15 秒，让新的服务主体有时间传遍 Azure Active Directory。 如果脚本等待时长不足，将显示错误，称“PrincipalNotFound: 主体 {id} 不存在于目录中”。
* 如果需要授予对更多订阅或资源组的服务主体访问权限，则再次针对不同的作用域运行 `New-AzureRMRoleAssignment` cmdlet。

### <a name="provide-certificate-through-automated-powershell-script"></a>通过自动执行的 PowerShell 脚本提供证书
以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure Active Directory 的实例。

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

应用程序 ID 和租户 ID 不敏感，因此可以直接将它们嵌入脚本中。 如果需要检索租户 ID，请使用：

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

如果需要检索应用程序 ID，请使用：

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>更改凭据

若要更改 AD 应用的凭据（为了保障安全或由于凭据过期的缘故），请使用 [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) 和 [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) cmdlet。

若要删除应用程序的所有凭据，请使用：

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

若要添加密码，请使用：

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

若要添加证书值，请按本主题所示创建自签名证书。 然后，使用：

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>保存访问令牌来简化登录
若要避免每次登录时都需要提供服务主体凭据，可保存访问令牌。

若要在以后的会话中使用当前访问令牌，请保存该配置文件。
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
打开该配置文件，并检查其内容。 请注意，它包含访问令牌。 无需再次手动登录，只需加载配置文件。
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> 访问令牌会过期，因此使用保存的配置文件仅适合在令牌有效期间使用。
>  

也可从要登录的 PowerShell 调用 REST 操作。 可以从身份验证响应中检索访问令牌，将其用于其他操作。 若要通过示例来了解如何通过调用 REST 操作来检索访问令牌，请参阅[生成访问令牌](resource-manager-rest-api.md#generating-an-access-token)。

## <a name="debug"></a>调试

创建服务主体时，可能会遇到以下错误：

* “Authentication_Unauthorized”或“在上下文中找不到订阅”。 - 如果帐户不具有在 Azure Active Directory 上注册应用[所需的权限](#required-permissions)，会收到此错误。 通常，当仅 Azure Active Directory 中的管理员用户可注册应用且帐户不是管理员帐户时，会看到此错误。 请要求管理员向你分配管理员角色，或让用户能够注册应用。

* 你的帐户“不具有对作用域‘/subscriptions/{guid}’执行操作‘Microsoft.Authorization/roleAssignments/write’的权限”。 - 当帐户不具有足够权限将角色分配给标识时，会看到此错误。 请要求订阅管理员将你添加到用户访问管理员角色。

## <a name="sample-applications"></a>示例应用程序
有关在不同平台上通过应用程序登录的信息，请参阅：

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>后续步骤
* 有关将应用程序集成到 Azure 以管理资源的详细步骤，请参阅 [Developer's guide to authorization with the Azure Resource Manager API](resource-manager-api-authentication.md)（使用 Azure Resource Manager API 进行授权的开发人员指南）。
* 有关应用程序和服务主体的详细说明，请参阅 [Application Objects and Service Principal Objects](../active-directory/active-directory-application-objects.md)（应用程序对象和服务主体对象）。 
* 有关 Azure Active Directory 身份验证的详细信息，请参阅 [Authentication Scenarios for Azure AD](../active-directory/active-directory-authentication-scenarios.md)（Azure AD 的身份验证方案）。
* 有关可对用户授予或拒绝的可用操作的列表，请参阅 [Azure Resource Manager 资源提供程序操作](../active-directory/role-based-access-control-resource-provider-operations.md)。


