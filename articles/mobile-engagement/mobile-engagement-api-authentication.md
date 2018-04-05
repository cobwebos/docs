---
title: 使用 Mobile Engagement REST API 进行身份验证
description: 介绍如何使用 Azure Mobile Engagement REST API 进行身份验证
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>使用 Mobile Engagement REST API 进行身份验证
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

## <a name="overview"></a>概述

本文档介绍如何获取有效的 Azure Active Directory (Azure AD) OAuth 令牌以使用 Mobile Engagement REST API 进行身份验证。

此过程假设你有一个有效的 Azure 订阅，并且已在某篇[开发人员教程](mobile-engagement-windows-store-dotnet-get-started.md)中创建了一个 Mobile Engagement 应用。

## <a name="authentication"></a>身份验证

使用基于 Microsoft Azure Active Directory 的 OAuth 令牌进行身份验证。 

若要对 API 请求进行身份验证，必须将授权标头添加到每个请求。 授权标头采用以下格式：

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory 令牌会在 1 小时后过期。
> 
> 

有多种方法可获取令牌。 由于从云服务中调用 API，因此需要使用 API 密钥。 在 Azure 术语中 API 密钥称为服务主体密码。 以下过程介绍一种手动设置的方法。

### <a name="one-time-setup-using-a-script"></a>一次性设置（使用脚本）

若要使用 PowerShell 脚本执行设置，请遵循以下说明中的步骤。 PowerShell 脚本花费最少的时间进行设置，但会使用大多数允许的默认值。 

或者，可以按照[手动安装](mobile-engagement-api-authentication-manual.md)中的说明，直接从 Azure 门户执行此操作。 如果从 Azure 门户进行设置，则可以执行更详细的配置。

1. [下载](http://aka.ms/webpi-azps)最新版本的 Azure PowerShell。 有关详细的下载说明，请参阅[此概述](/powershell/azure/overview)。

2. 安装 PowerShell 后，使用以下命令以确保已安装 **Azure 模块**：

    a. 请确保 Azure PowerShell 模块可在可用模块列表中找到。

        Get-Module –ListAvailable

    ![可用的 Azure 模块][1]

    b. 如果在上述列表中找不到 Azure PowerShell 模块，则需要运行以下命令：

        Import-Module Azure
3. 运行以下命令从 PowerShell 登录到 Azure 资源管理器。 提供 Azure 帐户的用户名和密码： 

        Login-AzureRmAccount
4. 如果有多个订阅，请执行以下步骤：

    a. 获取所有订阅的列表。 然后复制要使用的订阅的 **SubscriptionId**。 确保此订阅包含 Mobile Engagement 应用。 稍后将要使用此应用来与 API 交互。 

        Get-AzureRmSubscription

    b. 运行以下命令。 提供 **SubscriptionId** 用于配置要使用的订阅：

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. 将 [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 脚本的文本复制到本地计算机。 然后将它保存为 PowerShell cmdlet（例如 `APIAuth.ps1`），并运行它。

         `.\APIAuth.ps1`.

6. 该脚本要求为 **principalName** 提供输入。 提供要对 Active Directory 应用程序使用的合适名称（例如 APIAuth）。 

7. 该脚本完成运行后，会显示以下四个值。 请务必复制这些值，因为稍后使用 Active Directory 以编程方式进行身份验证时需要用到它们： 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **机密**

   将 TenantId 用作 `{TENANT_ID}`，将 ApplicationId 用作 `{CLIENT_ID}`，将 Secret 用作 `{CLIENT_SECRET}`。

   > [!NOTE]
   > 默认安全策略可能会阻止运行 PowerShell 脚本。 如果是这样，可以使用以下命令暂时配置执行策略，以允许脚本执行：
   > 
   > Set-ExecutionPolicy RemoteSigned
8. PowerShell cmdlet 集如下所示。
    ![PowerShell cmdlets][3]
9. 在 Azure 门户中，转到“Active Directory”，选择“应用注册”，并搜索你的应用以确保它存在。
    ![搜索应用][4]

### <a name="steps-to-get-a-valid-token"></a>获取有效令牌的步骤

1. 使用以下参数调用 API。 请务必替换 **TENANT\_ID**、**CLIENT\_ID** 和 **CLIENT\_SECRET**：
   
   * **请求 URL** 为 `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **HTTP Content-Type 标头** 为 `application/x-www-form-urlencoded`
   
   * **HTTP 请求正文** 为 `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    下面是示例请求：
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    下面是示例响应：
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     此示例包括 POST 参数的 URL 编码，其中的 `resource` 值实际是 `https://management.core.windows.net/`。 请注意，也要在 URL 中编码 `{CLIENT_SECRET}`，因为它可能包含特殊字符。

     > [!NOTE]
     > 如需测试，可以使用 [Fiddler](http://www.telerik.com/fiddler) 或 [Chrome Postman 扩展](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)等 HTTP 客户端工具。 
     > 
     > 
2. 现在，在每个 API 调用中都包括授权请求标头：
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    如果请求返回了 401 状态代码，请检查响应正文。 其中可能告知令牌已过期。 在这种情况下，请获取一个新令牌。

## <a name="use-the-apis"></a>使用 API
至此，拥有了一个有效的令牌，可以开始 API 调用。

1. 在每个 API 请求中，需要传递一个有效且未过期的令牌。 在上一部分中已获取未过期的令牌。

2. 将一些参数插入到用于标识应用程序的请求 URI。 请求 URI 如以下代码所示：
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    若要获取参数，请选择应用程序名称。 然后选择“仪表板”。 此时会显示包含所有三个参数的页面，如下所示：
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** 除非创建了一个新的资源组，否则资源组名称应该是 **MobileEngagement**。 

> [!NOTE]
> 忽略 API 根地址，因为它适用于以前的 API。
> 
> 如果使用 Azure 门户创建了该应用，则需要使用不同于应用名称本身的应用程序资源名称。 如果在 Azure 门户中创建了该应用，则应使用应用名称。 （在新门户中创建的应用的应用程序资源名称与应用名称之间没有差异。）
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



