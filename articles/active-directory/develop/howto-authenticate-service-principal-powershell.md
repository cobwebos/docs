---
title: 创建 Azure 应用标识（PowerShell） |Microsoft
titleSuffix: Microsoft identity platform
description: 介绍如何使用 Azure PowerShell 创建 Azure Active Directory 应用程序和服务主体，并通过基于角色的访问控制向其授予资源访问权限。 它演示如何使用证书对应用程序进行身份验证。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.date: 10/10/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: 8e428732fb49d27e3991071b87abee53b6e375b2
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648371"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>如何：使用 Azure PowerShell 创建具有证书的服务主体

当有应用或脚本需访问资源时，可以为应用设置一个标识，并使用其自己的凭据对应用进行身份验证。 此标识称为服务主体。 使用此方法能够：

* 将权限分配给应用标识，这些权限不同于自己的权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 执行无人参与的脚本时，使用证书进行身份验证。

> [!IMPORTANT]
> 请考虑使用 Azure 资源的托管标识作为应用程序标识，而不是创建服务主体。 如果代码在支持托管标识的服务上运行并访问支持 Azure Active Directory (Azure AD) 身份验证的资源，则托管标识是更好的选择。 若要详细了解 Azure 资源的托管标识（包括当前支持它的服务），请参阅[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md)。

本文演示如何创建使用证书进行身份验证的服务主体。 若要使用密码设置服务主体，请参阅[使用 Azure PowerShell 创建 Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)。

必须拥有[最新版本](/powershell/azure/install-az-ps) PowerShell 才能演练本文中的示例。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>所需的权限

若要完成本文，必须在 Azure AD 和 Azure 订阅中均有足够的权限。 具体而言，必须能够在 Azure AD 中创建应用并向角色分配服务主体。

检查帐户是否有足够权限的最简方法是使用门户。 请参阅[检查所需的权限](howto-create-service-principal-portal.md#required-permissions)。

## <a name="assign-the-application-to-a-role"></a>将应用程序分配给角色
要访问订阅中的资源，必须将应用程序分配到角色。 判定哪个角色能为应用程序提供适当的权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](/azure/role-based-access-control/built-in-roles)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的 "*读取*者" 角色意味着该应用程序可以读取该资源组及其包含的所有资源。 若要允许应用程序执行重启、启动和停止实例等操作，请选择 "*参与者*" 角色。

## <a name="create-service-principal-with-self-signed-certificate"></a>使用自签名证书创建服务主体

下面的示例介绍了简单的方案。 它使用[AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)创建具有自签名证书的服务主体，并使用[AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)将 "[读取](/azure/role-based-access-control/built-in-roles#reader)者" 角色分配给服务主体。 角色分配的范围限定为当前所选 Azure 订阅。 若要选择其他订阅，请使用 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。

> [!NOTE]
> New-selfsignedcertificate cmdlet 和 PKI 模块目前在 PowerShell Core 中不受支持。 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

该示例休眠 20 秒，让新的服务主体有时间传遍 Azure AD。 如果脚本等待时长不足，会显示错误：“主体 {ID} 不存在于目录 {DIR-ID} 中”。 若要解决此错误，请等待片刻，然后重新运行 **New-AzRoleAssignment** 命令。

可以使用 **ResourceGroupName** 参数将角色分配范围限制为特定资源组。 还可以使用 **ResourceType** 和 **ResourceName** 参数将范围限制为特定资源。 

如果**未使用 Windows 10 或 Windows Server 2016**，需要从 Microsoft 脚本中心下载[自签名证书生成器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)。 解压其内容，并导入所需的 cmdlet。

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

在脚本中，替换以下两行以生成证书。

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>通过自动执行的 PowerShell 脚本提供证书

以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure AD 的实例。

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>使用来自证书颁发机构的证书创建服务主体

以下示例使用证书颁发机构颁发的证书创建服务主体。 分配的范围限定为指定的 Azure 订阅。 它将服务主体添加到 "[读取](../../role-based-access-control/built-in-roles.md#reader)者" 角色。 如果在角色分配过程中发生错误，它会重试分配。

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

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>通过自动执行的 PowerShell 脚本提供证书
以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure AD 的实例。

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

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

应用程序 ID 和租户 ID 不敏感，因此可以直接将它们嵌入脚本中。 如果需要检索租户 ID，请使用：

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

如果需要检索应用程序 ID，请使用：

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>更改凭据

若要更改 AD 应用的凭据（由于安全性损害或凭据过期），请使用 [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) 和 [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) cmdlet。

若要删除应用程序的所有凭据，请使用：

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

要添加证书值，请按本文所示创建自签名证书。 然后，使用：

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>调试

创建服务主体时，可能会收到以下错误：

* “Authentication_Unauthorized”或“在上下文中找不到订阅”。 - 如果帐户不具有在 Azure AD 上注册应用[所需的权限](#required-permissions)，会看到此错误。 通常，仅当你的 Azure Active Directory 中的管理员用户可以注册应用，并且你的帐户不是管理员时，才会看到此错误。要求管理员为你分配管理员角色或允许用户注册应用。

* 帐户“不具有对作用域‘/subscriptions/{guid}’执行操作‘Microsoft.Authorization/roleAssignments/write’的权限”。 - 当帐户不具有足够权限将角色分配给标识时，会看到此错误。 请要求订阅管理员你将添加到用户访问管理员角色。

## <a name="next-steps"></a>后续步骤

* 若要使用密码设置服务主体，请参阅[使用 Azure PowerShell 创建 Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)。
* 有关应用程序和服务主体的详细说明，请参阅 [Application Objects and Service Principal Objects](app-objects-and-service-principals.md)（应用程序对象和服务主体对象）。
* 有关 Azure AD 身份验证的详细信息，请参阅 [Azure AD 的身份验证方案](authentication-scenarios.md)。
