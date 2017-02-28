---
title: "在 Azure 逻辑应用中调用自定义 API | Microsoft 文档"
description: "使用 Azure 逻辑应用调用 Azure 应用服务上托管的自定义 API"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: fc509ef8b30fadb6e026f346d4adbd6ef759624a
ms.openlocfilehash: 74aae9f757f56e94b583069a1fdee7efaafe467c
ms.lasthandoff: 02/16/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>使用 Azure 逻辑应用调用 Azure 应用服务上托管的自定义 API

虽然 Azure 逻辑应用提供了 40 多个用于各种服务的连接器，但是你可能希望调用可以运行你自己的代码的自定义 API。 Azure 应用服务提供了用于托管你自己的自定义 Web API 的最简单且可伸缩性最好的方法之一。 本文介绍了如何调用应用服务 API 应用、Web 应用或移动应用中托管的任何 Web API。
了解[如何在逻辑应用中将 API 生成为触发器或操作](../logic-apps/logic-apps-create-api-app.md)。

## <a name="deploy-your-web-app"></a>部署 Web 应用

首先，必须将 API 部署为 Azure 应用服务中的 Web 应用。 了解[创建 ASP.NET Web 应用时的基本部署](../app-service-web/web-sites-dotnet-get-started.md)。 虽然可以从逻辑应用中调用任何 API，但是为了获得最佳体验，我们建议添加 Swagger 元数据，以便与逻辑应用操作轻松集成。 了解如何[添加 Swagger 元数据](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)。

### <a name="api-settings"></a>API 设置

要使逻辑应用设计器能够解析你的 Swagger，必须为你的 Web 应用启用 CORS 并设置 API 定义属性。 

1.    在 Azure 门户中，选择你的 Web 应用。
2.    在打开的边栏选项卡中，找到“API”，并选择“API 定义”。 将“API 定义位置”设置为你的 swagger.json 文件的 URL。

    通常，此 URL 为 https://{name}.azurewebsites.net/swagger/docs/v1)。

3.    若要允许来自逻辑应用设计器的请求，请在“API”下选择“CORS”，然后为“*”设置一个 CORS 策略。

## <a name="call-into-your-custom-api"></a>调用自定义 API

在设置 CORS 和 API 定义属性后，应当能够轻松地在逻辑应用门户中向工作流添加自定义 API 操作。 在逻辑应用设计器中，可以浏览你的订阅网站来列出已定义了 Swagger URL 的网站。 还可以使用 HTTP + Swagger 操作来指向 Swagger 并列出可用的操作和输入。 最后，始终可以使用 HTTP 操作创建请求来调用任何 API，甚至包括没有或未公开 Swagger 文档的 API。

若要保护 API，有几种不同的方法可做到这一点：

*    无需更改代码。 可以使用 Azure Active Directory 保护 API，而无需更改任何代码或重新部署。
*    在 API 的代码中，强制实施基本身份验证、Azure Active Directory 身份验证或证书身份验证。

## <a name="secure-calls-to-your-api-without-changing-code"></a>在不更改代码的情况下保护对 API 的调用

在本部分中，你将创建两个 Azure Active Directory 应用程序 — 一个用于逻辑应用，另一个用于 Web 应用。 然后，使用与逻辑应用的 Azure Active Directory 应用关联的服务主体（客户端 ID 和密码）对 Web 应用的调用进行身份验证。 最后，在逻辑应用的定义中包含此应用程序 ID。

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>第 1 部分：设置逻辑应用的应用程序标识

逻辑应用将使用此应用程序标识根据 Azure Active Directory 进行身份验证。 你只需要为你的目录将此标识设置一次。 例如，可以选择为所有逻辑应用使用相同的标识，但是，也可以为每个逻辑应用创建唯一标识。 可以在 Azure 门户中或者使用 PowerShell 设置这些标识。

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>在 Azure 经典门户中创建应用程序标识

1. 在 Azure 经典门户中，转到“Azure Active Directory”。[](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) 
2. 选择要用于你的 Web 应用的目录。
3. 选择“应用程序”选项卡。 在页面底部的命令栏中，单击“添加”。
5. 为应用标识指定一个名称，单击下一步箭头。
6. 在“应用属性”下，输入一个格式化的唯一字符串作为域，然后单击对号。
7. 选择“配置”选项卡。 转到“客户端 ID”，然后复制客户端 ID 以在逻辑应用中使用。
8. 在“密钥”下，打开“选择持续时间”列表，然后为密钥选择持续时间。
9. 在页面底部，单击“保存”。 可能需要等待几秒钟。
10. 请确保复制目前出现在“密钥”下的密钥，以便可以在逻辑应用中使用此密钥。

#### <a name="create-the-application-identity-using-powershell"></a>使用 PowerShell 创建应用程序标识

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. 请务必复制所使用的**租户 ID**、**应用程序 ID** 和密码。

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>第 2 部分：使用 Azure Active Directory 应用标识保护 Web 应用

如果你的 Web 应用已部署，可以在 Azure 门户中启用授权。 否则，请启用 Azure Resource Manager 部署的授权部分。

#### <a name="enable-authorization-in-the-azure-portal"></a>在 Azure 门户中启用授权

1. 找到并选择你的 Web 应用。 在“设置”下，选择“身份验证/授权”。
2. 在“应用服务身份验证”下，开启身份验证，然后选择“保存”。

此时，自动创建了应用程序。 在第 3 部分中你需要使用此应用程序的客户端 ID，因此，必须执行以下步骤：

1. 在 Azure 经典门户中，转到“Azure Active Directory”。[](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)
2.    选择你的目录。
3. 在搜索框中，找到你的应用。
4. 在列表中，选择你的应用。
5. 选择“配置”选项卡，应当可以在其中看到“客户端 ID”。

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板部署 Web 应用

首先，必须为 Web 应用创建一个与用于逻辑应用的应用程序不同的应用程序。 首先按照上面的第 1 部分的步骤操作，但对于 **HomePage** 和 **IdentifierUris**，请使用你的 Web 应用的实际 https://**URL**。

> [!NOTE]
> 为 Web 应用创建应用程序时，必须使用 [Azure 经典门户](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)。 PowerShell commandlet 没有设置可让用户登录到网站的必需权限。

获得客户端 ID 和租户 ID 后，在部署模板中包括以下部分作为 Web 应用的子资源：

```
"resources": [
    {
        "apiVersion": "2015-08-01",
        "name": "web",
        "type": "config",
        "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
        "properties": {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

若要自动运行使用 Azure Active Directory 同时部署空白 Web 应用和逻辑应用的部署，请单击“部署到 Azure”：

[![部署到 Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

有关完整的模板，请参阅[逻辑应用调用在应用服务上托管的并受 Azure Active Directory 保护的自定义 API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)。

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>第 3 部分：填充逻辑应用中的授权部分

在 **HTTP** 操作的 **Authorization** 部分中：

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 元素 | 说明 |
| ------- | ----------- |
| type |身份验证的类型。 对于 ActiveDirectoryOAuth 身份验证，该值为 `ActiveDirectoryOAuth`。 |
| tenant |用于标识 AD 租户的租户标识符。 |
| audience |必需。 要连接到的资源。 |
| clientID |AD 应用程序的客户端标识符。 |
| secret |必需。 正在请求令牌的客户端的机密。 |

上面的模板已设置了此授权部分，但如果要直接对逻辑应用进行授权，则需要包括完整的授权部分。

## <a name="secure-your-api-in-code"></a>在代码中保护 API

### <a name="certificate-authentication"></a>证书身份验证

可以使用客户端证书验证传入到 Web 应用的请求。 有关设置代码的方法，请参阅[如何为 Web 应用配置 TLS 相互身份验证](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。

在 **Authorization** 部分中，应当包括以下信息： 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| 元素 | 说明 |
| ------- | ----------- |
| type |必需。 身份验证的类型。 对于 SSL 客户端证书，该值必须为 `ClientCertificate`。 |
| pfx |必需。 PFX 文件的 Base64 编码内容。 |
| password |必需。 用于访问 PFX 文件的密码。 |

### <a name="basic-authentication"></a>基本身份验证

若要验证传入的请求，可以使用基本身份验证，例如用户名和密码。 基本身份验证是一种常用模式，并且在用来生成你的应用的任何语言中都可以使用此身份验证。

在 **Authorization** 部分中，应当包括以下信息：

`{"type": "basic","username": "test","password": "test"}`。

| 元素 | 说明 |
| --- | --- |
| type |必需。 身份验证的类型。 对于基本身份验证，该值必须是 `Basic`。 |
| username |必需。 要进行身份验证的用户名。 |
| password |必需。 要进行身份验证的密码。 |

### <a name="handle-azure-active-directory-authentication-in-code"></a>在代码中处理 Azure Active Directory 身份验证

默认情况下，在 Azure 门户中启用的 Azure Active Directory 身份验证不提供细化的授权。 例如，此身份验证不会将 API 限定为仅供特定用户或应用使用，只会限定到特定的租户。

例如，若要在代码中将 API 限制为仅供你的逻辑应用使用，请提取具有 JWT 的标头。 检查调用方的标识，然后拒绝不匹配的请求。

更进一步，若要完全以自己的代码实现此身份验证，并且不使用 Azure 门户功能，请参阅[在 Azure 应用中使用本地 Active Directory 进行身份验证](../app-service-web/web-sites-authentication-authorization.md)。
若要为逻辑应用创建应用程序标识并使用该标识来调用 API，必须按照前面的步骤进行操作。
