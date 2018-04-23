---
title: 通过 PowerShell 为 Azure 应用创建标识 | Microsoft Docs
description: 介绍如何使用 Azure PowerShell 创建 Azure Active Directory 应用程序和服务主体，并通过基于角色的访问控制向其授予资源访问权限。 它演示如何使用证书对应用程序进行身份验证。
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
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: 70255ead4a556204689e9918b9c89e396f8122c0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>通过 Azure PowerShell 使用证书创建服务主体

当有应用或脚本需访问资源时，可以为应用设置一个标识，并使用其自己的凭据对应用进行身份验证。 此标识称为服务主体。 使用此方法能够：

* 将权限分配给应用标识，这些权限不同于自己的权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 执行无人参与的脚本时，使用证书进行身份验证。

> [!IMPORTANT]
> 请考虑使用 Azure AD 托管服务标识作为应用程序标识，而不是创建服务主体。 Azure AD MSI 是 Azure Active Directory 的公共预览版功能，它简化了创建代码标识的操作。 如果代码在支持 Azure AD MSI 的服务上运行并访问支持 Azure Active Directory 身份验证的资源，则 Azure AD MSI 是更好的选择。 若要详细了解 Azure AD MSI（包括哪些服务当前支持它），请参阅 [Azure 资源的托管服务标识](../active-directory/managed-service-identity/overview.md)。

本文演示如何创建使用证书进行身份验证的服务主体。 若要使用密码设置服务主体，请参阅[使用 Azure PowerShell 创建 Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)。

## <a name="required-permissions"></a>所需的权限

若要完成本文，必须在 Azure Active Directory 和 Azure 订阅中均有足够的权限。 具体而言，必须能够在 Azure Active Directory 中创建应用并向角色分配服务主体。

检查帐户是否有足够权限的最简方法是使用门户。 请参阅[检查所需的权限](resource-group-create-service-principal-portal.md#required-permissions)。

## <a name="create-service-principal-with-self-signed-certificate"></a>使用自签名证书创建服务主体

下面的示例介绍了简单的方案。 它使用 [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) 创建具有自签名证书的服务主体，并使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) 将[参与者](../role-based-access-control/built-in-roles.md#contributor)角色分配给该服务主体。 角色分配的范围限定为当前所选 Azure 订阅。 若要选择其他订阅，请使用 [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext)。

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

该示例休眠 20 秒，让新的服务主体有时间传遍 Azure Active Directory。 如果脚本等待时长不足，会显示错误：“主体 {ID} 不存在于目录 {DIR-ID} 中”。 若要解决此错误，请等待片刻，然后重新运行 **New-AzureRmRoleAssignment** 命令。

下一个示例更为复杂，因为它允许将角色分配的范围设置为不同于当前 Azure 订阅。 仅当希望将角色分配范围限定为某个资源组时指定 ResourceGroup 参数。 如果在角色分配过程中发生错误，它会重试分配。 必须在 Windows 10 或 Windows Server 2016 上使用 Azure PowerShell 2.0。

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

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -Subscription $SubscriptionId
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
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

如果**未使用 Windows 10 或 Windows Server 2016**，需要从 Microsoft 脚本中心下载[自签名证书生成器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)。 解压其内容，并导入所需的 cmdlet。

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

在脚本中，替换以下两行以生成证书。

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -StoreName My `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>通过自动执行的 PowerShell 脚本提供证书

以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure Active Directory 的实例。

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
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
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

以下示例使用证书颁发机构颁发的证书创建服务主体。 分配的范围限定为指定的 Azure 订阅。 它将服务主体添加到 [参与者](../role-based-access-control/built-in-roles.md#contributor) 角色。 如果在角色分配过程中发生错误，它会重试分配。

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

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

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
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
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

要添加证书值，请按本文所示创建自签名证书。 然后，使用：

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>调试

创建服务主体时，可能会收到以下错误：

* “Authentication_Unauthorized”或“在上下文中找不到订阅”。 - 如果帐户不具有在 Azure Active Directory 上注册应用[所需的权限](#required-permissions)，会收到此错误。 通常，当仅 Azure Active Directory 中的管理员用户可注册应用且帐户不是管理员帐户时，会看到此错误。请要求管理员向你分配管理员角色，或让用户能够注册应用。

* 帐户“不具有对作用域‘/subscriptions/{guid}’执行操作‘Microsoft.Authorization/roleAssignments/write’的权限”。 - 当帐户不具有足够权限将角色分配给标识时，会看到此错误。 请要求订阅管理员你将添加到用户访问管理员角色。

## <a name="next-steps"></a>后续步骤
* 若要使用密码设置服务主体，请参阅[使用 Azure PowerShell 创建 Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)。
* 有关将应用程序集成到 Azure 以管理资源的详细步骤，请参阅[使用 Azure 资源管理器 API 进行授权的开发人员指南](resource-manager-api-authentication.md)。
* 有关应用程序和服务主体的详细说明，请参阅 [Application Objects and Service Principal Objects](../active-directory/active-directory-application-objects.md)（应用程序对象和服务主体对象）。 
* 有关 Azure Active Directory 身份验证的详细信息，请参阅 [Authentication Scenarios for Azure AD](../active-directory/active-directory-authentication-scenarios.md)（Azure AD 的身份验证方案）。
