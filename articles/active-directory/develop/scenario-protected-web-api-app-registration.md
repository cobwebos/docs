---
title: 受保护的 web API-应用注册 |Azure
description: 了解如何构建一个受保护的 Web API 和你需要注册该应用程序的信息。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111127"
---
# <a name="protected-web-api---app-registration"></a>受保护的 web API-应用注册

本文介绍了受保护的 web API 的应用程序注册具体信息。

请参阅[快速入门：注册一个应用程序的 Microsoft 标识平台](quickstart-register-app.md)有关如何注册应用程序的常见步骤。

## <a name="accepted-token-version"></a>接受令牌的版本

Microsoft 标识平台终结点可以颁发两种类型的令牌： v1.0 令牌和 v2.0 令牌。 您可以了解有关在这些令牌的详细信息[访问令牌](access-tokens.md)。 接受令牌的版本取决于**支持的帐户类型**创建你的应用程序时选择：

- 如果的值**支持的帐户类型**是**中的任何组织的目录和个人 Microsoft 帐户 (例如 Skype，Xbox、 Outlook.com) 帐户**，接受令牌的版本将为 v2.0。
- 否则，接受令牌的版本将 v1.0。

一旦你创建应用程序，你可以接受令牌版本通过执行以下步骤：

1. 在 Azure 门户中，选择您的应用程序，然后选择**清单**为你的应用。
2. 在清单中，搜索 **"accessTokenAcceptedVersion"** ，并确保其值是**2**。 此属性允许 Azure AD 知道 web API 接受 v2.0 令牌。 如果它是**null**，接受令牌的版本将为 1.0 版。
3. 选择“保存”。 

> [!NOTE]
> 它是对 web API 来决定它接受令牌版本 （1.0 版或 v2.0）。 当客户端请求的 web API 使用 Microsoft 标识平台 v2.0 终结点的标记时，他们将获得一个标记，指示 web API 接受的版本。

## <a name="no-redirect-uri"></a>没有重定向 URI

无需注册的重定向 URI，因为没有用户以交互方式是登录的 web Api。

## <a name="expose-an-api"></a>公开一个 API

特定于 web Api 的另一个设置是公开的 API 和公开作用域。

### <a name="resource-uri-and-scopes"></a>资源 URI 和作用域

作用域的形式通常是`resourceURI/scopeName`。 Microsoft Graph 的作用域具有类似的快捷方式`User.Read`，但此字符串是一种方便的`https://graph.microsoft.com/user.read`。

在应用程序注册期间你将需要定义以下参数：

- 默认情况下，应用程序注册门户的资源 URI-建议你使用`api://{clientId}`。 此资源 URI 是唯一的但它不是人工可读。 可以更改它，但请确保它是唯一的。
- 一个或多个**作用域**(客户端应用程序，它们将显示为**委派权限**为 Web API)
- 一个或多个**应用程序角色**(客户端应用程序，它们将显示为**应用程序权限**为 Web API)

作用域也会提供给最终用户使用你的应用程序的许可屏幕上显示。 因此，您将需要提供描述作用域的相应字符串：

- 最终用户查看
- 查看租户管理员，可以授予管理员同意

### <a name="how-to-expose-delegated-permissions-scopes"></a>如何公开委派的权限 （作用域）

1. 选择**公开一个 API**应用程序注册中的部分和：
   1. 选择“添加范围”。 
   1. 如果请求，则接受建议的应用程序 ID URI (api:// {clientId}) 通过选择**保存并继续**。
   1. 输入以下参数：
      - 有关**作用域名称**，使用`access_as_user`。
      - 有关**人员便可以同意**，请确保**管理员和用户**选择选项。
      - 在中**管理员许可显示名称**，类型`Access TodoListService as a user`。
      - 在中**管理员许可说明**，类型`Accesses the TodoListService Web API as a user`。
      - 在中**用户许可显示名称**，类型`Access TodoListService as a user`。
      - 在中**用户许可说明**，类型`Accesses the TodoListService Web API as a user`。
      - 保持**状态**设置为**已启用**。
      - 选择**添加作用域**。

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>后台应用程序调用 Web API 是其中的用例

在本段中，您将学习如何注册受保护的 Web API，以便可以由后台程序应用程序安全地调用：

- 你将需要公开**应用程序权限**。 仅将声明应用程序权限，如守护程序应用程序并不与用户交互，因此委托的权限是没有意义。
- 租户管理员可能需要颁发令牌的 Azure AD，只有已注册他们想要访问一个 Web API 应用权限的应用程序到 Web 应用。

#### <a name="how-to-expose-application-permissions-app-roles"></a>如何公开应用程序权限 （应用角色）

若要公开应用程序权限，你将需要编辑清单。

1. 在你的应用程序的应用程序注册中，单击**清单**。
1. 通过定位编辑清单`appRoles`设置并添加一个或多个应用程序角色。 下面的示例 JSON 块提供了角色定义。  将保留`allowedMemberTypes`为"Application"仅。 请确保**id**是唯一的 guid 和**displayName**并**值**不包含任何空格。
1. 保存清单。

内容`appRoles`应如下所示 (`id`可以是任何唯一的 GUID)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>如何确保，Azure AD 颁发的令牌对 Web api 只能与允许客户端

Web API 会检查应用程序角色 （这是执行此操作时的开发人员方法）。 但是，您甚至可以配置 Azure Active Directory 对 Web api 只与以前批准的租户管理员才能访问你的 API 的应用程序颁发的令牌。 若要添加此附加的安全性：

1. 在应用上**概述**应用注册页上，选择超链接并使用在应用程序的名称**托管在本地目录中的应用程序**。 此字段的标题可以会被截断。 例如，可以阅读： `Managed application in ...`

   > [!NOTE]
   >
   > 当您选择将导航到此链接**企业应用程序概述**与你在其中创建它的租户中的应用程序的服务主体相关联的页。 可以使用浏览器的后退按钮导航回应用注册页。

1. 选择**属性**页面**管理**部分中的企业应用程序页
1. 如果你想要强制实施到 Web API 中只有某些客户端访问权限的 AAD，设置**需要进行用户分配？** 到**是**。

   > [!IMPORTANT]
   >
   > 通过设置**需要进行用户分配？** 到**是**，Web API 的请求访问令牌时，AAD 将检查客户端的应用角色分配。 如果已将客户端分配给任何 AppRoles，AAD 将只返回以下错误： `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > 如果将保留**需要进行用户分配？** 到**否**，<span style='background-color:yellow; display:inline'>客户端请求访问令牌为 Web API 时，Azure AD 不会检查应用角色分配</span>。 因此，任何守护程序客户端 （这是使用客户端凭据流的任何客户端） 将仍将能够为 API 获取访问令牌，只需通过指定它的受众。 任何应用程序，将能够访问 API，而无需请求它的权限。 现在，这不是然后结束，因为 Web API 可以始终下, 一节中所述验证该应用程序通过验证的访问令牌是否具有具有正确角色 （这由租户管理员授权），`roles`声称和正确的值为 t他声明 (在本例中`access_as_application`)。

1. 选择“保存” 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序的代码配置](scenario-protected-web-api-app-configuration.md)
