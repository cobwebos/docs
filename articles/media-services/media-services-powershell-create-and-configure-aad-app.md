---
title: "使用 PowerShell 创建 Azure AD 应用程序以访问 Azure 媒体服务 API | Microsoft Docs"
description: "了解如何使用 PowerShell 创建 Azure Active Directory (Azure AD) 应用程序，并安装该应用程序以访问 Azure 媒体服务 API。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: f1d7ad94686d633b6353f39cef91d1cb1efab773
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---

# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>使用 PowerShell 创建 Azure AD 应用程序以用于 Azure 媒体服务 API

了解如何使用 PowerShell 脚本创建 Azure Active Directory (Azure AD) 应用程序和服务主体，以访问 Azure 媒体服务资源。  

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，可先创建一个 [Azure 免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。 
- 一个媒体服务帐户。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。
- Azure PowerShell 版本 0.8.8 或更高版本。 有关详细信息，请参阅[如何使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
- Azure Resource Manager cmdlets。  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>使用 PowerShell 创建 Azure AD 应用程序  

```powershell
Login-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

有关详细信息，请参阅以下文章：

- [使用 Azure PowerShell 创建服务主体来访问资源](../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [使用 Azure PowerShell 管理基于角色的访问控制](../active-directory/role-based-access-control-manage-access-powershell.md)
- [如何使用证书手动配置守护程序应用](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>后续步骤

开始[将文件上传到你的帐户](media-services-portal-upload-files.md)。

