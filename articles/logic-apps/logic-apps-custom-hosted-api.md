---
title: "为 Azure 逻辑应用部署、调用和验证 Web API 和 REST API | Microsoft Docs"
description: "为与 Azure 逻辑应用进行系统集成，在工作流中部署、验证和调用 Web API 和 REST API"
keywords: "Web API, REST API, 连接器, 工作流, 系统集成, 身份验证"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>为逻辑应用将自定义 API 作为连接器进行部署、调用和验证

[创建自定义 API](./logic-apps-create-api-app.md)以提供在逻辑应用工作流中使用的操作或触发器后，必选先部署 API 才能调用。 虽然可从逻辑应用调用任何 API，但为获得最佳体验，请添加 [Swagger 元数据](http://swagger.io/specification/)，用于说明 API 的操作和参数。 该 Swagger 文件有助于使 API 运行更顺畅，与逻辑应用集成更轻松。

虽然可以将 API 部署为 [Web 应用](../app-service-web/app-service-web-overview.md)，但请考虑将 API 部署为 [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)，便于更加轻松地在云端和本地生成、托管和使用 API。 不必更改 API 中的任何代码 - 可直接将代码部署到 API 应用。 可在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 上托管API，前者是一款平台即服务 (PaaS) 产品，可为 API 托管提供一种最简单且可缩放性最高的最佳方法。

若要验证逻辑应用对 API 的调用，可在 Azure 门户中设置 Azure Active Directory，这样便无需更新代码。 或者，还可利用 API 代码要求并强制执行身份验证。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>将 API 部署为 Web 应用或 API 应用

若要从逻辑应用调用自定义 API，首先需将 API 作为 Web 应用或 API 应用部署到 Azure App Service。 此外，若要使逻辑应用设计器能够读取 Swagger 文档，请为 Web 应用或 API 应用设置 API 定义属性并开启[跨域资源共享 (CORS)](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig)。

1. 在 Azure 门户中，选择你的 Web 应用或 API 应用。

2. 在打开的边栏选项卡的“API”下方，选择“API 定义”。 将“API 定义位置”设置为你的 swagger.json 文件的 URL。

   通常，URL 的格式为：`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![链接到自定义 API 的 Swagger 文件](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. 在“API”下，选择“CORS”。 将“允许的来源”的 CORS 策略设置为 **'*'**（允许所有）。

   此设置允许来自逻辑应用设计器的请求。

   ![允许从逻辑应用设计器向自定义 API 发送请求](media/logic-apps-custom-hosted-api/custom-api-cors.png)

有关详细信息，请参阅以下文章：

* [添加适用于 ASP.NET Web API 的 Swagger 元数据](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [将 ASP.NET Web API 部署到 Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>从逻辑应用工作流调用自定义 API

设置 API 定义属性和 CORS 后，应该可以将自定义 API 的触发器和操作包含在逻辑应用工作流中。 

*  若要查看具有 Swagger URL 的网站，可在逻辑应用设计器中浏览订阅网站。

*  若要通过指向 Swagger 文档查看可用操作和输入，请使用 [HTTP + Swagger 操作](../connectors/connectors-native-http-swagger.md)。

*  无论调用任何 API（包括不具有或未公开 Swagger 文档的 API），始终可以使用 [HTTP 操作](../connectors/connectors-native-http.md)创建请求。

## <a name="authenticate-calls-to-your-custom-api"></a>验证对自定义 API 的调用

通过以下方式可以保护对自定义 API 的调用：

* [无需更改代码](#no-code)：通过 Azure 门户使用 [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) 保护 API，以便无需更新代码或重新部署 API。

  > [!NOTE]
  > 默认情况下，在 Azure 门户中开启的 Azure AD 身份验证不提供细化的授权。 例如，此身份验证将 API 锁定到特定租户，而不是特定用户或应用。 

* [更新 API 代码](#update-code)：通过代码强制执行[证书身份验证](#certificate)、[基本身份验证](#basic)或 [Azure AD 身份验证](#azure-ad-code)，以此保护 API。

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>在不更改代码的情况下，验证对 API 的调用

下面是此方法的常规步骤：

1. 创建两个 [Azure Active Directory (Azure AD) 应用程序标识](../app-service-api/app-service-api-dotnet-service-principal-auth.md)：一个用于逻辑应用，另一个用于 Web 应用（或 API 应用）。

2. 若要验证对 API 的调用，请对与逻辑应用的 Azure AD 应用程序标识相关联的[服务主体](../app-service-api/app-service-api-dotnet-service-principal-auth.md)使用凭据（客户端 ID 和密码）。

3. 在逻辑应用定义中包含此应用程序 ID。

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>第 1 部分：为逻辑应用创建 Azure AD 应用程序标识

逻辑应用将使用此 Azure AD 应用程序标识根据 Azure AD 进行身份验证。 只需为目录将此标识设置一次。 例如，可选择为所有逻辑应用使用相同标识，但也可为每个逻辑应用创建唯一标识。 可在 Azure 门户、[Azure 经典门户](#app-identity-logic-classic)中设置这些标识或者使用 [PowerShell](#powershell)。

**在 Azure 门户中为逻辑应用创建应用程序标识**

1. 在 [Azure 门户](https://portal.azure.com "https://portal.azure.com")中，选择“Azure Active Directory”。 

2. 确认所在目录与 Web 应用或 API 应用相同。

   > [!TIP]
   > 若要切换目录，请单击配置文件，然后选择其他目录。 还可以选择“概述” > “切换目录”。

3. 在目录菜单的“管理”下，选择“应用注册” > “新建应用程序注册”。

   > [!TIP]
   > 默认情况下，应用注册列表显示目录中的所有应用注册。 若要仅查看应用注册，请在搜索框旁选择“我的应用”。 

   ![新建应用注册](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. 为应用程序标识命名，将“应用程序类型”设置为“Web 应用/API”，提供格式化的唯一字符串，作为“登录 URL”的域，然后选择“创建”。

   ![提供应用程序标识的名称和登录 URL](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   你为逻辑应用创建的应用程序标识现在将出现在应用注册列表中。

   ![逻辑应用的应用程序标识](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. 在应用注册列表中，选择新的应用程序标识。 复制并保存“应用程序 ID”，在第 3 部分中将其用作逻辑应用的“客户端 ID”。

   ![复制并保存逻辑应用的应用程序 ID](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. 如果未显示应用程序标识设置，请选择“设置”或“所有设置”。

7. 在“API 访问”下，选择“密钥”。 在“说明”下，提供密钥的名称。 在“过期时间”下，选择密钥的持续时间。

   创建的密钥将充当逻辑应用的应用程序标识的“机密”或密码。

   ![为逻辑应用标识创建密钥](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. 在工具栏上，选择“保存”。 密钥现在将显示在“值”的下方。 
务必复制并保存密钥以供后续使用，因为离开密钥边栏选项卡后密钥将会隐藏。

   在第 3 部分配置逻辑应用时，将此密钥指定为“机密”或密码。

   ![复制并保存密钥以供后续使用](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**在 Azure 经典门户中为逻辑应用创建应用程序标识**

1. 在 Azure 经典门户中，选择“[Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)”。

2. 选择用于 Web 应用或 API 应用的同一目录。

3. 在“应用程序”选项卡上，选择页面底部的“添加”。

4. 为应用程序标识提供名称，然后选择“下一步”（向右箭头）。

5. 在“应用属性”下，提供格式化的唯一字符串，作为“登录 URL”和“应用 ID URI”的域，然后选择“完成”（复选标记）。

6. 在“配置”选项卡上，复制并保存逻辑应用的“客户端 ID”，以便在第 3 部分使用。

7. 在“密钥”下，打开“选择持续时间”列表。 选择密钥的持续时间。

   创建的密钥将充当逻辑应用的应用程序标识的“机密”或密码。

8. 在页面底部，选择“保存”。 可能需要等待几秒钟。

9. 在“密钥”下，务必复制并保存当前显示的密钥。 

   在第 3 部分配置逻辑应用时，将此密钥指定为“机密”或密码。

有关详细信息，请了解如何[将应用服务应用程序配置为使用 Azure Active Directory 登录](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。

<a name="powershell"></a>

**在 PowerShell 中为逻辑应用创建应用程序标识**

可使用 PowerShell 通过 Azure Resource Manager 执行此任务。 在 PowerShell 中运行以下命令：

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. 请务必复制所使用的“租户 ID”（Azure AD 租户的 GUID）、“应用程序 ID”和密码。

有关详细信息，请了解如何[使用 PowerShell 创建服务主体以访问资源](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>第 2 部分：为 Web 应用或 API 应用创建 Azure AD 应用程序标识

如果已部署 Web 应用或 API 应用，则可在 Azure 门户中开启身份验证并创建应用程序标识。 否则，可以[在使用 Azure Resource Manager 模板部署时开启身份验证](#authen-deploy)。 

**在 Azure 门户中为已部署的应用创建应用程序标识并开启身份验证**

1. 在 [Azure 门户](https://portal.azure.com "https://portal.azure.com")中，找到并选择你的 Web 应用或 API 应用。 

2. 在“设置”下，选择“身份验证/授权”。 在“应用服务身份验证”下，“开启”身份验证。 在“身份验证提供程序”下，选择“Azure Active Directory”。

   ![开启身份验证](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. 现在为 Web 应用或 API 应用创建应用程序标识，如此处所示。 在“Azure Active Directory 设置”边栏选项卡中，将“管理模式”设置为“快速”。 选择“新建 AD 应用”。 为应用程序标识命名，然后选择“确定”。 

   ![为 Web 应用或 API 应用创建应用程序标识](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. 在“身份验证/授权”边栏选项卡中，选择“保存”。

现在必须找到与 Web 应用或 API 应用关联的应用程序标识的客户端 ID 和租户 ID。 第 3 部分需使用这些 ID。 因此，请对Azure 门户或 [Azure 经典门户](#find-id-classic)继续执行这些步骤。

**在 Azure 门户中查找 Web 应用或 API 应用的应用程序标识的客户端 ID 和租户 ID**

1. 在“身份验证提供程序”下，选择“Azure Active Directory”。 

   ![选择“Azure Active Directory”](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. 在“Azure Active Directory 设置”边栏选项卡中，将“管理模式”设置为“高级”。

3. 复制“客户端 ID”并保存该 GUID，以便在第 3 部分使用。

   > [!TIP] 
   > 如果“客户端 ID”和“颁发者 URL”未显示，请尝试刷新 Azure 门户，然后重复步骤 1。

4. 在“颁发者 URL”下，复制并保存 GUID，以便在第 3 部分使用。 如有必要，还可在 Web 应用或 API 应用的部署模板中使用此 GUID。

   此 GUID 是你的特定租户的 GUID（“租户 ID”），应显示在此 URL 中：`https://sts.windows.net/{GUID}`

5. 不要保存更改，关闭“Azure Active Directory 设置”边栏选项卡。

<a name="find-id-classic"></a>

**在 Azure 经典门户中，查找 Web 应用或 API 应用的应用程序标识的客户端 ID 和租户 ID**

1. 在 Azure 经典门户中，选择“[Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)”。

2.  选择用于 Web 应用或 API 应用的目录。

3. 在“搜索”框中，查找并选择 Web 应用或 API 应用的应用程序标识。

4. 在“配置”选项卡上，复制“客户端 ID”并保存该 GUID，以便在第 3 部分使用。

5. 获取客户端 ID 后，在“配置”选项卡底部选择“查看终结点”。

6. 复制“联合元数据文档”的 URL，然后浏览到该 URL。

7. 在打开的元数据文档中，查找根 EntityDescriptor ID 元素，它的 entityID 属性的格式为：`https://sts.windows.net/{GUID}` 

      此属性中的 GUID 就是你的特定租户的 GUID（租户 ID）。

8. 复制并保存该租户 ID，以便在第 3 部分使用，必要时还可在 Web 应用或 API 应用的部署模板中使用。

有关详细信息，请参阅以下主题：

* [Azure App Service 中 API 应用的用户身份验证](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Azure 应用服务中的身份验证和授权](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**使用 Azure Resource Manager 模板部署时开启身份验证**

还必须为 Web 应用或 API 应用创建 Azure AD 应用程序标识，该标识不能与逻辑应用的应用标识相同。 若要创建应用程序标识，请在 Azure 门户中按照先前在第 2 部分中的步骤操作。 也可以按照第 1 部分的步骤操作，但对于“登录 URL”和“应用 ID URI”，请确保使用 Web 应用或 API 应用的实际 `https://{URL}`。 执行这些步骤时，请务必保存客户端 ID 和租户 ID，以便在应用的部署模板和第 3 部分使用。

> [!NOTE]
> 为 Web 应用或 API 应用创建 Azure AD 应用程序标识时，必须使用 Azure 门户或 Azure 经典门户，而不是 PowerShell。 PowerShell commandlet 没有设置可让用户登录到网站的必需权限。

获得客户端 ID 和租户 ID 后，将这些 ID 作为 Web 应用或 API 应用的子资源包含在部署模板中：

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
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

若要使用 Azure Active Directory 身份验证自动同时部署空白 Web 应用和逻辑应用，请[在此处查看完整模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)或单击此处的“部署到 Azure”：

[![部署到 Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>第 3 部分：填充逻辑应用中的授权部分

上面的模板已设置了此授权部分，但如果要直接对逻辑应用进行授权，则必须包括完整的授权部分。

在代码视图中打开逻辑应用定义，转到“HTTP”操作部分，找到“授权”部分，然后将此行包含在其中：

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| 元素 | 说明 |
| ------- | ----------- |
| tenant |Azure AD 租户的 GUID |
| audience |必需。 你想要访问的目标资源的 GUID - Web 应用或 API 应用的应用程序标识中的客户端 ID |
| clientId |请求访问权限的客户端的 GUID - 逻辑应用的应用程序标识中的客户端 ID |
| secret |必需。 请求访问令牌的客户端的应用程序标识中的密钥或密码 |
| type |身份验证类型。 对于 ActiveDirectoryOAuth 身份验证，该值为 `ActiveDirectoryOAuth`。 |

例如：

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>通过代码保护 API 调用

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>证书身份验证

若要验证从逻辑应用传入 Web 应用或 API 应用的请求，可以使用客户端证书。 若要设置代码，请了解[如何配置 TLS 相互身份验证](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。

在“授权”部分中包含此行： 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| 元素 | 说明 |
| ------- | ----------- |
| type |必需。 身份验证类型。 对于 SSL 客户端证书，该值必须为 `ClientCertificate`。 |
| password |必需。 用于访问客户端证书（PFX 文件）的密码 |
| pfx |必需。 客户端证书（PFX 文件）的 Base64 编码内容 |

<a name="basic"></a>

#### <a name="basic-authentication"></a>基本身份验证

若要验证从逻辑应用传入 Web 应用或 API 应用的请求，可以使用基本身份验证，如用户名和密码。 基本身份验证是一种常用模式，在用来生成你的 Web 应用或 API 应用的任何语言中都可以使用此身份验证。

在“授权”部分中包含此行：

`{"type": "basic", "username": "username", "password": "password"}`。

| 元素 | 说明 |
| --- | --- |
| type |必需。 身份验证类型。 对于基本身份验证，该值必须是 `Basic`。 |
| username |必需。 用于进行身份验证的用户名 |
| password |必需。 用于进行身份验证的密码 |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>利用代码进行 Azure Active Directory 身份验证

默认情况下，在 Azure 门户中开启的 Azure AD 身份验证不提供细化的授权。 例如，此身份验证将 API 锁定到特定租户，而不是特定用户或应用。 

若要通过代码限制 API 访问逻辑应用，请提取具有 JSON Web 令牌 (JWT) 的标头。 检查调用方的标识，然后拒绝不匹配的请求。

更进一步，若要完全以自己的代码实现此身份验证，并且不使用 Azure 门户，请了解如何[在 Azure 应用中使用本地 Active Directory 进行身份验证](../app-service-web/web-sites-authentication-authorization.md)。

若要为逻辑应用创建应用程序标识并使用该标识来调用 API，必须按照前面的步骤进行操作。

## <a name="next-steps"></a>后续步骤

* [利用诊断日志和警报检查逻辑应用性能](logic-apps-monitor-your-logic-apps.md)
