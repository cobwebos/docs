---
title: "将身份验证添加到自定义 API - Azure 逻辑应用 | Microsoft Docs"
description: "通过逻辑应用为对自定义 API 的调用设置身份验证"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 2528f4318d92bbfdc1008795876f0240a5e3e4f6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="secure-calls-to-your-custom-apis-from-logic-apps"></a>通过逻辑应用保护对自定义 API 的调用

要保护对 API 的调用，可通过 Azure 门户设置 Azure Active Directory (Azure AD) 身份验证，这样便无需更新代码。 或者，还可利用 API 代码要求并强制执行身份验证。

## <a name="authentication-options-for-your-api"></a>适用于 API 的身份验证选项

通过以下方式可以保护对自定义 API 的调用：

* [无需更改代码](#no-code)：通过 Azure 门户使用 [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) 保护 API，以便无需更新代码或重新部署 API。

  > [!NOTE]
  > 默认情况下，在 Azure 门户中开启的 Azure AD 身份验证不提供细化的授权。 例如，此身份验证将 API 锁定到特定租户，而不是特定用户或应用。 

* [更新 API 代码](#update-code)：通过代码强制执行[证书身份验证](#certificate)、[基本身份验证](#basic)或 [Azure AD 身份验证](#azure-ad-code)，以此保护 API。

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>在不更改代码的情况下，验证对 API 的调用

下面是此方法的常规步骤：

1. 创建两个 Azure Active Directory (Azure AD) 应用程序标识：一个用于逻辑应用，另一个用于 Web 应用（或 API 应用）。

2. 要验证对 API 的调用，请对与逻辑应用的 Azure AD 应用程序标识相关联的服务主体使用凭据（客户端 ID 和密码）。

3. 在逻辑应用定义中包含此应用程序 ID。

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>第 1 部分：为逻辑应用创建 Azure AD 应用程序标识

逻辑应用将使用此 Azure AD 应用程序标识根据 Azure AD 进行身份验证。 只需为目录将此标识设置一次。 例如，可选择为所有逻辑应用使用相同标识，但也可为每个逻辑应用创建唯一标识。 可以在 Azure 门户中或者使用 [PowerShell](#powershell) 设置这些标识。

**在 Azure 门户中为逻辑应用创建应用程序标识**

1. 在 [Azure 门户](https://portal.azure.com "https://portal.azure.com")中，选择“Azure Active Directory”。 

2. 确认所在目录与 Web 应用或 API 应用相同。

   > [!TIP]
   > 要切换目录，请选择配置文件，然后选择其他目录。 还可以选择“概述” > “切换目录”。

3. 在目录菜单的“管理”下，选择“应用注册” > “新建应用程序注册”。

   > [!TIP]
   > 默认情况下，应用注册列表显示目录中的所有应用注册。 若要仅查看应用注册，请在搜索框旁选择“我的应用”。 

   ![新建应用注册](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. 为应用程序标识命名，将“应用程序类型”设置为“Web 应用/API”，提供格式化的唯一字符串，作为“登录 URL”的域，然后选择“创建”。

   ![提供应用程序标识的名称和登录 URL](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   你为逻辑应用创建的应用程序标识现在将出现在应用注册列表中。

   ![逻辑应用的应用程序标识](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. 在应用注册列表中，选择新的应用程序标识。 复制并保存“应用程序 ID”，在第 3 部分中将其用作逻辑应用的“客户端 ID”。

   ![复制并保存逻辑应用的应用程序 ID](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. 如果未显示应用程序标识设置，请选择“设置”或“所有设置”。

7. 在“API 访问”下，选择“密钥”。 在“说明”下，提供密钥的名称。 在“过期时间”下，选择密钥的持续时间。

   创建的密钥将充当逻辑应用的应用程序标识的“机密”或密码。

   ![为逻辑应用标识创建密钥](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. 在工具栏上，选择“保存”。 密钥现在将显示在“值”的下方。 
务必复制并保存密钥以供后续使用，因为离开“密钥”页面后密钥将隐藏。

   在第 3 部分配置逻辑应用时，将此密钥指定为“机密”或密码。

   ![复制并保存密钥以供后续使用](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**在 PowerShell 中为逻辑应用创建应用程序标识**

可使用 PowerShell 通过 Azure 资源管理器执行此任务。 在 PowerShell 中运行以下命令：

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. 请务必复制所使用的“租户 ID”（Azure AD 租户的 GUID）、“应用程序 ID”和密码。

有关详细信息，请了解如何[使用 PowerShell 创建服务主体以访问资源](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>第 2 部分：为 Web 应用或 API 应用创建 Azure AD 应用程序标识

如果已部署 Web 应用或 API 应用，则可在 Azure 门户中开启身份验证并创建应用程序标识。 否则，可以[在使用 Azure 资源管理器模板部署时开启身份验证](#authen-deploy)。 

**在 Azure 门户中为已部署的应用创建应用程序标识并开启身份验证**

1. 在 [Azure 门户](https://portal.azure.com "https://portal.azure.com")中，找到并选择你的 Web 应用或 API 应用。 

2. 在“设置”下，选择“身份验证/授权”。 在“应用服务身份验证”下，“开启”身份验证。 在“身份验证提供程序”下，选择“Azure Active Directory”。

   ![开启身份验证](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. 现在为 Web 应用或 API 应用创建应用程序标识，如此处所示。 在“Azure Active Directory 设置”页面，将“管理模式”设置为“快速”。 选择“新建 AD 应用”。 为应用程序标识命名，然后选择“确定”。 

   ![为 Web 应用或 API 应用创建应用程序标识](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. 在“身份验证/授权”页上，选择“保存”。

现在必须找到与 Web 应用或 API 应用关联的应用程序标识的客户端 ID 和租户 ID。 第 3 部分需使用这些 ID。 因此，请对 Azure 门户继续执行这些步骤。

**在 Azure 门户中查找 Web 应用或 API 应用的应用程序标识的客户端 ID 和租户 ID**

1. 在“身份验证提供程序”下，选择“Azure Active Directory”。 

   ![选择“Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. 在“Azure Active Directory 设置”页面，将“管理模式”设置为“高级”。

3. 复制“客户端 ID”并保存该 GUID，以便在第 3 部分使用。

   > [!TIP] 
   > 如果“客户端 ID”和“颁发者 URL”未显示，请尝试刷新 Azure 门户，然后重复步骤 1。

4. 在“颁发者 URL”下，复制并保存 GUID，以便在第 3 部分使用。 如有必要，还可在 Web 应用或 API 应用的部署模板中使用此 GUID。

   此 GUID 是你的特定租户的 GUID（“租户 ID”），应显示在此 URL 中：`https://sts.windows.net/{GUID}`

5. 在不保存更改的情况下，关闭“Azure Active Directory 设置”页面。

<a name="authen-deploy"></a>

**使用 Azure 资源管理器模板部署时开启身份验证**

还必须为 Web 应用或 API 应用创建 Azure AD 应用程序标识，该标识不能与逻辑应用的应用标识相同。 若要创建应用程序标识，请在 Azure 门户中按照先前在第 2 部分中的步骤操作。 

也可以按照第 1 部分的步骤操作，但对于“登录 URL”和“应用 ID URI”，请确保使用 Web 应用或 API 应用的实际 `https://{URL}`。 执行这些步骤时，请务必保存客户端 ID 和租户 ID，以便在应用的部署模板和第 3 部分使用。

> [!NOTE]
> 为 Web 应用或 API 应用创建 Azure AD 应用程序标识时，必须使用 Azure 门户，而不是 PowerShell。 PowerShell commandlet 没有设置可让用户登录到网站的必需权限。

获得客户端 ID 和租户 ID 后，将这些 ID 作为 Web 应用或 API 应用的子资源包含在部署模板中：

``` json
"resources": [ {
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
} ]
```

若要使用 Azure Active Directory 身份验证自动同时部署空白 Web 应用和逻辑应用，请[在此处查看完整模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)或单击此处的“部署到 Azure”：

[![部署到 Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>第 3 部分：填充逻辑应用中的授权部分

上面的模板已设置了此授权部分，但如果要直接对逻辑应用进行授权，则必须包括完整的授权部分。

在代码视图中打开逻辑应用定义，转到“HTTP”操作部分，找到“授权”部分，然后将此行包含在其中：

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| 元素 | 必选 | 说明 | 
| ------- | -------- | ----------- | 
| tenant | 是 | Azure AD 租户的 GUID | 
| audience | 是 | 想要访问的目标资源的 GUID - Web 应用或 API 应用的应用程序标识中的客户端 ID | 
| clientId | 是 | 请求访问权限的客户端的 GUID - 逻辑应用的应用程序标识中的客户端 ID | 
| secret | 是 | 请求访问令牌的客户端的应用程序标识中的密钥或密码 | 
| type | 是 | 身份验证类型。 对于 ActiveDirectoryOAuth 身份验证，该值为 `ActiveDirectoryOAuth`。 | 
|||| 

例如：

``` json
{
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

若要验证从逻辑应用传入 Web 应用或 API 应用的请求，可以使用客户端证书。 若要设置代码，请了解[如何配置 TLS 相互身份验证](../app-service/app-service-web-configure-tls-mutual-auth.md)。

在“授权”部分中包含此行： 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| 元素 | 必选 | 说明 | 
| ------- | -------- | ----------- | 
| type | 是 | 身份验证类型。 对于 SSL 客户端证书，该值必须为 `ClientCertificate`。 | 
| password | 是 | 用于访问客户端证书（PFX 文件）的密码 | 
| pfx | 是 | 客户端证书（PFX 文件）的 base64 编码内容 | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>基本身份验证

若要验证从逻辑应用传入 Web 应用或 API 应用的请求，可以使用基本身份验证，如用户名和密码。 基本身份验证是一种常用模式，在用来生成你的 Web 应用或 API 应用的任何语言中都可以使用此身份验证。

在“授权”部分中包含此行：

`{"type": "basic", "username": "username", "password": "password"}`。

| 元素 | 必选 | 说明 | 
| ------- | -------- | ----------- | 
| type | 是 | 要使用的身份验证类型。 对于基本身份验证，该值必须是 `Basic`。 | 
| username | 是 | 要用于身份验证的用户名 | 
| password | 是 | 要用于身份验证的密码 | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>利用代码进行 Azure Active Directory 身份验证

默认情况下，在 Azure 门户中开启的 Azure AD 身份验证不提供细化的授权。 例如，此身份验证将 API 锁定到特定租户，而不是特定用户或应用。 

若要通过代码限制 API 访问逻辑应用，请提取具有 JSON Web 令牌 (JWT) 的标头。 检查调用方的标识，并拒绝不匹配的请求。

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>后续步骤

* [从逻辑应用工作流部署和调用自定义 API](../logic-apps/logic-apps-custom-api-host-deploy-call.md)