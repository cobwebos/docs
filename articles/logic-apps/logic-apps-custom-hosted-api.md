---
title: "在 Azure 逻辑应用调用自定义 API | Microsoft Docs"
description: "使用由逻辑应用托管的自定义 API"
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
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 32a2b4435761e8bc26c184ee00613539e4639552


---
# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>在逻辑应用中使用在应用服务中托管的自定义 API
虽然逻辑应用具有许多（40 多个）用于各种服务的连接器，但是你可能想要调用自定义 API，以便运行自己的代码。 托管自己的自定义 Web 应用的最简单且最灵活的方式之一是使用应用服务。 本文介绍如何调用应用服务 API 应用、Web 应用或移动应用中托管的任何 Web API。

有关在逻辑应用中生成 API 并将其用作触发器或操作的信息，请查阅[这篇文章](../logic-apps/logic-apps-create-api-app.md)。

## <a name="deploy-your-web-app"></a>部署 Web 应用
首先，需要将 API 部署为应用服务中的 Web 应用。 以下说明包含了基本的部署方法：[创建 ASP.NET Web 应用](../app-service-web/web-sites-dotnet-get-started.md)。  尽管可以从逻辑应用中调用任何 API，但是为了获得最佳体验，我们建议添加 Swagger 元数据，以便与逻辑应用操作轻松集成。  可以在[添加 swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui) 中查阅详细信息。

### <a name="api-settings"></a>API 设置
若要使用逻辑应用设计器来解析 Swagger，请务必启用 CORS，并设置 Web 应用的 APIDefinition 属性。  在 Azure 门户中可以非常容易地设置。  只需打开 Web 应用的“设置”边栏选项卡，在 API 部分下面将“API 定义”设为 swagger.json 文件的 URL（通常为 https://{name}.azurewebsites.net/swagger/docs/v1），并对“*”添加 CORS 策略以允许来自逻辑应用设计器的请求。

## <a name="calling-into-the-api"></a>调用 API
在逻辑应用门户中时，如果已经设置 CORS 和 API 定义属性，就可以轻松地在流中添加自定义 API 操作。  在设计器中可以选择浏览订阅网站，从而列出已定义 swagger URL 的网站。  你还可以使用 HTTP + Swagger 操作来指向 swagger 并列出可用的操作和输入。  最后，可以始终使用 HTTP 操作创建请求以调用任何 API，甚至包括没有或未公开 swagger 文档的 API。

如果想要保护 API，有几种不同的方法可做到这一点：

1. 无需更改代码 — 可以使用 Azure Active Directory 保护 API，而无需更改任何代码或重新部署。
2. 在 API 代码中实施基本身份验证、AAD 身份验证或证书身份验证。

## <a name="securing-calls-to-your-api-without-a-code-change"></a>安全调用 API 而无需更改代码
本节创建两个 Azure Active Directory 应用程序 — 一个用于逻辑应用，另一个用于 Web 应用。  可以使用与用于逻辑应用的 AAD 应用程序关联的服务主体（客户端 ID 和密码）对 Web 应用的调用进行身份验证。 最后，在逻辑应用的定义中包含此应用程序 ID。

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>第 1 部分：设置逻辑应用的应用程序标识
逻辑应用使用此标识对 active directory 进行身份验证。 只需对目录执行此操作一次。 例如，可以选择对所有逻辑应用使用相同的标识，但是如果你想要，还可以为每个逻辑应用创建唯一标识。 可以在 UI 中或使用 PowerShell 执行此操作。

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>使用 Azure 经典门户创建应用程序标识
1. 导航到 [Azure 经典门户中的 Active directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，选择用于 Web 应用的目录
2. 单击“应用程序”选项卡。
3. 在页面底部的命令栏中，单击“添加”。
4. 为标识命名，单击下一步箭头
5. 在两个文本框中输入以域为格式的唯一字符串，单击对勾
6. 单击应用程序的“配置”选项卡。
7. 复制“客户端 ID”，此 ID 将用于逻辑应用
8. 在“密钥”部分单击“选择持续时间”，选择 1 年或 2 年
9. 单击屏幕底部的“保存”按钮（可能需要等待几秒钟）
10. 现在请务必复制框中的密钥。 此密钥也将在逻辑应用中使用

#### <a name="create-the-application-identity-using-powershell"></a>使用 PowerShell 创建应用程序标识
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. 请务必复制所使用的**租户 ID**、**应用程序 ID** 和密码

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>第 2 部分：使用 AAD 应用程序标识保护 Web 应用
如果已部署 Web 应用，只需在门户中启用它。 否则，请启用 Azure Resource Manager 部署的授权部分。

#### <a name="enable-authorization-in-the-azure-portal"></a>在 Azure 门户中启用授权
1. 导航到 Web 应用，单击命令栏中的“设置”。
2. 单击“授权/身份验证”。
3. 将其设置为“开”。

此时，自动创建了应用程序。 你需要在第 3 部分中使用应用程序的客户端 ID，因此，需要执行以下操作：

1. 转到 [Azure 经典门户中的 Active directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，选择目录。
2. 在搜索框中搜索应用
3. 在列表中单击它
4. 单击“配置”选项卡。
5. 此时可看到“客户端 ID”

#### <a name="deploying-your-web-app-using-an-arm-template"></a>使用 ARM 模板部署 Web 应用
首先，需要为 Web 应用创建应用程序。 此应用程序不同于用于逻辑应用的应用程序。 首先按照上面的第 1 部分的步骤操作，之后针对**主页**和 **IdentifierUris** 则使用 Web 应用的实际的 https://**URL**。

> [!NOTE]
> 为 Web 应用创建应用程序时，必须使用 [Azure 经典门户方法](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，因为 PowerShell commandlet 未设置用户登录到网站所需的权限。
> 
> 

具备客户端 ID 和租户 ID 后，在部署模板中包括以下内容作为 Web 应用的子资源：

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

若要自动运行使用 AAD 同时部署空白 Web 应用和逻辑应用的部署，请单击以下按钮：

[![部署到 Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

有关完整的模板，请参阅[逻辑应用调用在应用服务上托管并受 AAD 保护的自定义 API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)。

### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>第 3 部分：填充逻辑应用中的授权部分
在 **HTTP** 操作的“授权”部分：`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 元素 | 说明 |
| --- | --- |
| type |身份验证的类型。 对于 ActiveDirectoryOAuth 身份验证，该值为 ActiveDirectoryOAuth。 |
| tenant |用于标识 AD 租户的租户标识符。 |
| audience |必需。 要连接到的资源。 |
| clientID |AD 应用程序的客户端标识符。 |
| secret |必需。 正在请求令牌的客户端的机密。 |

上述模板已对此进行设置，但是如果要直接授权逻辑应用，则需要包括完整的授权部分。

## <a name="securing-your-api-in-code"></a>在代码中保护 API
### <a name="certificate-auth"></a>证书身份验证
可以使用客户端证书验证 Web 应用的传入请求。 有关如何设置代码，请参阅[如何为 Web 应用配置 TLS 相互身份验证](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。

在“授权”部分提供：`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`。

| 元素 | 说明 |
| --- | --- |
| type |必需。 身份验证的类型。对于 SSL 客户端证书，该值必须为 ClientCertificate。 |
| pfx |必需。 PFX 文件的 Base64 编码内容。 |
| password |必需。 用于访问 PFX 文件的密码。 |

### <a name="basic-auth"></a>基本身份验证
可以使用基本身份验证（例如用户名和密码）来验证传入请求。 基本身份验证是一种常用模式，可以在使用任何语言生成的应用中执行。

在“授权”部分提供：`{"type": "basic","username": "test","password": "test"}`。

| 元素 | 说明 |
| --- | --- |
| type |必需。 身份验证的类型。 对于基本身份验证，该值必须为 Basic。 |
| username |必需。 要进行身份验证的用户名。 |
| password |必需。 要进行身份验证的密码。 |

### <a name="handle-aad-auth-in-code"></a>在代码中处理 AAD 身份验证
默认情况下，在门户中启用的 Azure Active Directory 身份验证不进行细化的授权。 例如，它不会将 API 的访问限定为特定用户或应用，而只是特定的租户。

如果要将 API 的访问限定为仅逻辑应用，则可以在代码中提取包含 JWT 的标头，并检查调用方是谁，并拒绝任何不匹配的请求。

进一步说，如果要完全在自己的代码中实现此身份验证，而不使用门户功能，则可以阅读以下文章：[在 Azure App Service 中使用 Active Directory 身份验证](../app-service-web/web-sites-authentication-authorization.md)。

你仍然需要按照上述步骤创建逻辑应用的应用程序标识，并使用它来调用 API。




<!--HONumber=Jan17_HO3-->


