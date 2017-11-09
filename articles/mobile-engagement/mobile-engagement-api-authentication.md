---
title: "使用 Mobile Engagement REST API 进行身份验证"
description: "介绍如何使用 Azure Mobile Engagement REST API 进行身份验证"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: b05181d9252c0a804648e01b4058019278ae5abe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>使用 Mobile Engagement REST API 进行身份验证
## <a name="overview"></a>概述
本文档介绍如何获取有效的 AAD Oauth 令牌以使用 Mobile Engagement REST API 进行身份验证。 

假设你有一个有效的 Azure 订阅，并且已使用我们的一个[开发人员教程](mobile-engagement-windows-store-dotnet-get-started.md)创建了一个 Mobile Engagement 应用。

## <a name="authentication"></a>身份验证
使用基于 Microsoft Azure Active Directory 的 OAuth 令牌进行身份验证。 

为了对 API 请求进行身份验证，必须将授权标头添加到每个请求，其格式如下：

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory 令牌会在 1 小时后过期。
> 
> 

有多种方法可获取令牌。 由于通常从云服务中调用 API，因此需要使用 API 密钥。 在 Azure 术语中 API 密钥称为服务主体密码。 以下过程介绍一种手动设置的方法。

### <a name="one-time-setup-using-script"></a>一次性设置（使用脚本）
应遵循以下说明，使用 PowerShell 脚本执行安装程序，这会花费最少的时间进行安装，但会使用最多允许的默认值。 或者，可以按照[手动安装](mobile-engagement-api-authentication-manual.md)中的说明，直接从 Azure 门户执行此操作，并进行优化配置。 

1. 从[此处](http://aka.ms/webpi-azps)获取最新版本的 Azure PowerShell。 有关下载说明的详细信息，可以访问此[链接](/powershell/azure/overview)。  
2. 安装 Azure PowerShell 后，使用以下命令以确保已安装 **Azure 模块**：
   
    a. 请确保 Azure PowerShell 模块可在可用模块列表中找到。 
   
        Get-Module –ListAvailable 
   
    ![可用的 Azure 模块][1]
   
    b. 如果在上述列表中找不到 Azure PowerShell 模块，则需要运行以下命令：
   
        Import-Module Azure 
3. 通过运行以下命令，并提供 Azure 帐户的用户名和密码，从 PowerShell 登录到 Azure Resource Manager，： 
   
        Login-AzureRmAccount
4. 如果有多个订阅，则应运行以下命令：
   
    a. 获取所有订阅的列表，并复制要使用的订阅的 SubscriptionId。 请确保此订阅具有要使用 API 与之进行交互的 Mobile Engagement 应用的同一个订阅。 
   
        Get-AzureRmSubscription
   
    b. 运行以下命令，提供 SubscriptionId，以配置要使用的订阅。
   
        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. 将 [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 脚本的文本复制到本地计算机，并将其保存为 PowerShell cmdlet（例如 `APIAuth.ps1`），然后执行 `.\APIAuth.ps1`。 
6. 该脚本将要求为 **principalName** 提供输入。 在此处提供你想使用的合适名称，以创建 Active Directory 应用程序（例如 APIAuth）。 
7. 在脚本完成后，它会显示以下四个值，需要以编程方式使用 AD 进行身份验证，因此，请务必复制它们。 
   
    **TenantId**、**SubscriptionId**、**ApplicationId** 和 **Secret**。
   
    将使用 TenantId 作为 `{TENANT_ID}`、ApplicationId 作为 `{CLIENT_ID}`、Secret 作为 `{CLIENT_SECRET}`。
   
   > [!NOTE]
   > 默认安全策略可能会阻止你运行 PowerShell 脚本。 如果是这样，可以使用以下命令，暂时配置执行策略以允许脚本执行：
   > 
   > Set-ExecutionPolicy RemoteSigned
   > 
   > 
8. PS cmdlet 集应类似于如下所示。 
   
    ![][3]
9. 查看 Azure 管理门户，会有一个新的 AD 应用程序是使用提供给“**显示我公司拥有的应用程序**”下名为 **principalName** 的脚本名称创建的。
   
    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>获取有效令牌的步骤
1. 使用以下参数调用 API，并确保替换 TENANT\_ID、CLIENT\_ID 和 CLIENT\_SECRET：
   
   * **请求 URL** 作为 *https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
   * **HTTP 内容类型标头**作为 *application/x-www-form-urlencoded*
   * **HTTP 请求正文**作为 *grant\_type=client\_credentials&client_id={CLIENT\_ID}&client_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*
     
     下面是示例请求：
     
       POST /{TENANT_ID}/oauth2/token HTTP/1.1   Host: login.microsoftonline.com   Content-Type: application/x-www-form-urlencoded   grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso   urce=https%3A%2F%2Fmanagement.core.windows.net%2F
     
     下面是示例响应：
     
       HTTP/1.1 200 OK   Content-Type: application/json; charset=utf-8   Content-Length: 1234
     
       {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144   5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
     
     此示例包括 POST 参数的 URL 编码，`resource` 值实际是 `https://management.core.windows.net/`。 请注意，也要在 URL 中编码 `{CLIENT_SECRET}`，因为它可能包含特殊字符。
     
     > [!NOTE]
     > 如需测试，可以使用诸如 [Fiddler](http://www.telerik.com/fiddler) 或 [Chrome Postman 扩展插件](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)的HTTP 客户端工具 
     > 
     > 
2. 现在，在每个 API 调用中都包括授权请求标头：
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    如果返回了 401 状态代码，请检查响应正文，其中可能会告诉你令牌已过期。 在这种情况下，请获取一个新令牌。

## <a name="using-the-apis"></a>使用 API
至此，拥有了一个有效的令牌，可以开始 API 调用。

1. 在每个 API 请求中，需要传递在上节中获得的有效且未过期的令牌。
2. 需要将某些参数插入到请求 URI，用于标识应用程序。 请求 URI 应类似如下所示
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    要获取参数，请单击应用程序名称并单击仪表板，会看到如下页面和所有 3 个参数。
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** 除非创建了一个新的资源组，否则资源组名称应该是 **MobileEngagement**。 
     
     ![Mobile Engagement API URI 参数][2]

> [!NOTE]
> <br/>
> 
> 1. 忽略 API 根地址，因为这适用于以前的 API。<br/>
> 2. 如果使用 Azure 经典门户创建了该应用，则需要使用不同于应用程序名称自身的应用程序资源名称。 如果在 Azure 门户中创建了该应用，则应使用应用的自身名称（在新门户中创建的应用的应用程序资源名称与应用名称之间没有差异）。  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



