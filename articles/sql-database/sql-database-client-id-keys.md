---
title: "获取对应用程序进行身份验证所需的值以便从代码访问 SQL 数据库 | Microsoft 文档"
description: "创建服务主体以便从代码访问 SQL 数据库。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
tags: 
ms.assetid: b43e43bb-6660-49e6-b069-abde97eb5770
ms.service: sql-database
ms.custom: development
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 6fb71859d0ba2e0f2b39d71edd6d518b7a03bfe9
ms.openlocfilehash: 321b1630680f8bd4271f863b2cbe39be1a00cb89


---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>获取对应用程序进行身份验证所需的值以便从代码访问 SQL 数据库
要在代码中创建并管理 SQL 数据库，必须在创建 Azure 资源的订阅中的 Azure Active Directory (AAD) 域内注册你的应用。

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>创建服务主体以便从应用程序访问资源
需要安装并运行最新的 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx)。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

以下 PowerShell 脚本创建 Active Directory (AD) 应用程序和服务主体，我们需要对 C# 应用进行身份验证。 该脚本输出我们需要用于前面 C# 示例的值。 有关详细信息，请参阅 [使用 Azure PowerShell 创建服务主体以访问资源](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>另请参阅
* [使用 C# 创建 SQL 数据库](sql-database-get-started-csharp.md)
* [使用 Azure Active Directory 身份验证连接到 SQL 数据库](sql-database-aad-authentication.md)




<!--HONumber=Feb17_HO3-->


