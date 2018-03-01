---
title: "使用 Azure AD 保护自定义连接器 - Azure 逻辑应用 | Microsoft Docs"
description: "使用 Azure Active Directory (Azure AD) 将安全性和身份验证添加到 API 与连接器"
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
ms.openlocfilehash: dae76a4230a1cfbe8970bbb1015041fde7765f49
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD) 将安全性添加到 API 与连接器

若要保护 API 与自定义连接器之间的调用，可将 Azure AD 身份验证添加到 Web API 和连接器。 对于此方案，需创建两个 Azure Active Directory (Azure AD) 应用 - 一个 Azure AD 应用保护 Web API，另一个 Azure AD 应用保护连接器注册并添加委托访问权限。 

本教程使用 Azure 资源管理器 API 作为示例。 Azure 资源管理器可帮助管理在 Azure 中生成的解决方案（例如数据库、虚拟机和 Web 应用）的组件。 若要从工作流管理 Azure 资源，Azure 资源管理器的自定义连接器可能会发挥作用。 有关详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以从[免费的 Azure 帐户](https://azure.microsoft.com/free/)着手。 或者，也可注册[即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 本教程使用 [Azure 资源管理器的示例 OpenAPI 文件](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > 示例 OpenAPI 文件未定义所有的 Azure 资源管理器操作，目前仅包含[列出所有订阅](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List)的操作。 可以使用 [OpenAPI 在线编辑器](http://editor.swagger.io/)编辑此 OpenAPI 或创建另一个 OpenAPI 文件。
  >
  > 在想要使用 Azure AD 身份验证的任何 RESTful API 中，都可以运用本教程所述的内容。

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1.创建第一个 Azure AD 应用来保护 Web API

当自定义连接器调用 API（在本示例中为 Azure 资源管理器 API）时，第一个 Azure AD 应用会执行身份验证。

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果有多个 Azure Active Directory 租户，请检查用户名下的目录，确认已登录到正确的目录。 

   ![确认目录](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > 若要更改目录，请选择自己的用户名，并选择所需的目录。

2. 在 Azure 主菜单中，选择“Azure Active Directory”，以便可以查看当前目录。

   ![选择“Azure Active Directory”](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > 如果 Azure 主菜单中未显示“Azure Active Directory”，请选择“所有服务”。 在“筛选”框中，键入“Azure Active Directory”作为筛选器，选择“Azure Active Directory”。

3. 在目录菜单中的“管理”下，选择“应用注册”。 在已注册应用列表中，选择“+ 新建应用程序注册”。

   ![依次选择“应用注册”、“+ 新建应用程序注册”](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. 在“创建”下，按表中所述为 Azure AD 应用提供详细信息，并选择“创建”。 

   ![创建 Azure AD 应用](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *web-api-app-name* | Web API 的 Azure AD 应用的名称 | 
   | **应用程序类型** | **Web 应用/API** | 应用的类型 | 
   | **登录 URL** | `https://login.windows.net` | | 
   |||| 

5. 返回到目录的“应用注册”列表后，选择自己的 Azure AD 应用。

   ![从列表中选择自己的 Azure AD 应用](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. 出现该应用的详细信息页时，请确保**将应用的“应用程序 ID”复制并保存到某个安全位置**。 稍后需要使用此 ID。

   ![保存“应用程序 ID”以备后用](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. 现在，提供 Azure AD 应用的回复 URL。 在应用的“设置”菜单中，选择“回复 URL”。 输入此 URL，选择“保存”。

   ![回复 URL](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **回复 URL** | 对于自己的 API，请输入以下 URL： </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **委托权限** | {不需要} | | 
   | **客户端密钥** | {不需要} | | 
   |||| 

   > [!TIP]
   > 如果前面未显示“设置”菜单，请按如下所示选择“设置”：
   >
   > ![选择“设置”](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2.为自定义连接器创建第二个 Azure AD 应用

第二个 Azure AD 应用保护自定义连接器注册，并添加对第一个 Azure AD 应用所保护的 Web API 的委托访问权限。 

> [!IMPORTANT]
> 请确保将这两个 Azure AD 应用放在同一目录中。

1. 返回到“应用注册”列表，再次选择“+ 新建应用程序注册”。

   ![依次选择“应用注册”、“+ 新建应用程序注册”](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. 在“创建”下，按表中所述为第二个 Azure AD 应用提供详细信息，并选择“创建”。 

   ![创建 Azure AD 应用](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *your-connector-name* | 连接器的 Azure AD 应用的名称 | 
   | **应用程序类型** | **Web 应用/API** | 应用的类型 | 
   | **登录 URL** | `https://login.windows.net` | | 
   |||| 

3. 返回到目录的“应用注册”列表后，选择第二个 Azure AD 应用。

   ![从列表中选择第二个 Azure AD 应用](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. 出现此应用的详细信息页时，同样请确保**将此应用的“应用程序 ID”复制并保存到某个安全位置**。 稍后需要使用此 ID。

   ![保存“应用程序 ID”以备后用](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. 现在，提供 Azure AD 应用的回复 URL。 在应用的“设置”菜单中，选择“回复 URL”。 输入此 URL，选择“保存”。

   | 设置 | 建议的值 | 
   | ------- | --------------- | 
   | **回复 URL** | 对于 Azure 资源管理器自定义连接器，请输入以下 URL：`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > 如果前面未显示“设置”菜单，请按如下所示选择“设置”：
   >
   > ![选择“设置”](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. 返回到“设置”菜单中，选择“所需权限” > “添加”。

   ![“所需权限”>“添加”](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. 当 **添加 API 访问权限** 菜单打开时，请选择 **选择 API** > 
**Windows Azure 服务管理 API** > **选择**。

   ![选择 API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. 在“添加 API 访问权限”菜单中，选择“选择权限”。 在“委托权限”下，选择“以组织用户身份访问 Azure 服务管理” > “选择”。

   ![选择“委托权限”>“以组织用户身份访问 Azure 服务管理”](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   对于其他选项：

   | 选项 | 建议的值 | 说明 | 
   | ------ | --------------- | ----------- | 
   | **委托权限** | | 选择对 Web API 的委托访问权限 | 
   |||| 

9. 现在，请在“添加 API 访问权限”菜单中选择“完成”。

   ![“添加 API 访问权限”菜单 >“完成”](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. 现在，为连接器的 Azure AD 应用生成客户端密钥（或“机密”）。 

    1. 返回到“设置”菜单，选择“密钥”。 
    为密钥提供不超过 16 个字符的名称，选择失效期限，并选择“保存”。

       ![创建客户端密钥](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. 显示所生成的密钥后，**请确保将此密钥复制并保存到某个安全位置**，然后退出“密钥”页。
    
       ![手动复制并保存密钥](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. 保存密钥后，可以放心关闭“设置”菜单。

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3.将 Azure AD 身份验证添加到 Web API 应用

现在，请在第一个 Azure AD 应用中为 Web API 启用身份验证。 有关详细信息，请参阅有关[如何将应用服务应用程序配置为使用 Azure Active Directory 登录](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)的文章。

1. 在 [Azure 门户](https://portal.azure.com)中，找到之前在[从 Web API 创建自定义连接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)中所发布的 Web API 应用。

2. 在“设置”下，选择“身份验证/授权”。 

   1. 在“应用服务身份验证”下，选择“打开”。
   2. 对于“请求未经身份验证时需执行的操作”，请选择“使用 Azure Active Directory 登录”。
   3. 对于“身份验证提供程序”，请选择“Azure Active Directory”。 

   ![选择“身份验证/授权”](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. 在“Azure Active Directory 设置”页上：

   1. 在“管理模式”下，选择“Express”。

   2. 现在，选择 Web API 应用用来进行身份验证的 Azure AD 应用。 
   选择“选择现有 AD 应用” > “Azure AD 应用”。

   3. 在“Azure AD 应用程序”下，选择前面为 Web API 应用创建的 Azure AD 应用。 
   
   4. 一直选择“确定”，直到返回“身份验证/授权”页。

   例如：

   ![选择代表 Web API 应用的 Azure AD 应用](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. 在“身份验证/授权”页上，选择“保存”。

   ![保存身份验证设置](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   现在，Web API 应用可以使用 Azure AD 进行身份验证。

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4.在 Web API 的 OpenAPI 文件中设置 Azure AD 身份验证

打开 Web API 应用的 OpenAPI 文件，以便可以添加 `securityDefintions` 对象，并在 `host` 属性下添加 Azure AD 身份验证。 以下示例显示了 `host` 属性和 `securityDefinitions` 对象：

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
接下来，可以创建并注册自定义连接器了。

## <a name="next-steps"></a>后续步骤

* [注册自定义连接器](../logic-apps/logic-apps-custom-connector-register.md)
* [自定义连接器常见问题解答](../logic-apps/custom-connector-faq.md)
