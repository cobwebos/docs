---
title: 受保护的 Web API - 应用注册 | Azure
description: 了解如何构建一个受保护的 web API 和你需要注册该应用程序的信息。
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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536863"
---
# <a name="protected-web-api-app-registration"></a>受保护的 web API:应用注册

本文介绍了受保护的 web api 的应用注册的具体情况。

请参阅[快速入门：注册一个应用程序的 Microsoft 标识平台](quickstart-register-app.md)注册应用的常见步骤。

## <a name="accepted-token-version"></a>接受的令牌版本

Microsoft 标识平台终结点可以发出两种类型的令牌：v1.0 令牌和 v2.0 令牌。 有关这些令牌的详细信息，请参阅[访问令牌](access-tokens.md)。 接受令牌的版本取决于**支持的帐户类型**创建你的应用程序时选择：

- 如果的值**支持的帐户类型**是**中的任何组织的目录和个人 Microsoft 帐户 (例如 Skype，Xbox、 Outlook.com) 帐户**，接受令牌的版本将为 v2.0。
- 否则，接受令牌的版本将 v1.0。

创建应用程序后，您可以确定或通过执行以下步骤更改接受令牌的版本：

1. 在 Azure 门户中选择应用，然后选择应用的“清单”  。
2. 在清单中，搜索 **"accessTokenAcceptedVersion"** 。 请注意，其值是**2**。 此属性指定到 Azure Active Directory (Azure AD) web API 接受 v2.0 令牌。 如果值为**null**，接受令牌的版本是 1.0 版。
3. 如果已更改的令牌的版本，请选择**保存**。

> [!NOTE]
> Web API 指定接受的令牌的版本 （1.0 版或 v2.0）。 当客户端请求从 Microsoft 标识平台 v2.0 终结点的 web API 的令牌时，他们将获得一个标记，指示 web API 接受的版本。

## <a name="no-redirect-uri"></a>无重定向 URI

Web Api 不需要注册的重定向 URI，因为没有用户以交互方式登录。

## <a name="expose-an-api"></a>公开 API

特定于 Web API 的另一设置是公开的 API 和公开的作用域

### <a name="resource-uri-and-scopes"></a>资源 URI 和作用域

作用域的形式通常是`resourceURI/scopeName`。 Microsoft Graph 的作用域具有快捷方式等`User.Read`。 此字符串是一种快捷方式`https://graph.microsoft.com/user.read`。

在应用程序注册期间你将需要定义这些参数：

- 资源 URI。 默认情况下，应用程序注册门户建议你使用`api://{clientId}`。 此资源 URI 独一无二，但不是人工可读的。 可以更改它，但请确保新的值是唯一的。
- 一个或多个*作用域*。 (客户端应用程序，它们将显示为*委派权限*web API。)
- 一个或多个*应用程序角色*。 (客户端应用程序，它们将显示为*应用程序权限*web API。)

作用域也会提供给您的应用程序的最终用户许可屏幕上显示。 因此，您需要提供描述作用域的相应字符串：

- 最终用户查看。
- 查看租户管理员，可以授予管理员同意。

### <a name="exposing-delegated-permissions-scopes"></a>公开委派的权限 （作用域）

1. 选择**公开一个 API**部分中的应用程序注册。
1. 选择“添加范围”。 
1. 如果系统提示，请接受建议的应用程序 ID URI (`api://{clientId}`) 通过选择**保存并继续**。
1. 输入这些参数：
      - 有关**作用域名称**，使用**access_as_user**。
      - 有关**人员便可以同意**，请确保**管理员和用户**处于选中状态。
      - 在中**管理员许可显示名称**，输入**以用户身份访问 TodoListService**。
      - 在中**管理员许可说明**，输入**访问 TodoListService Web API 以用户身份**。
      - 在中**用户许可显示名称**，输入**以用户身份访问 TodoListService**。
      - 在中**用户许可说明**，输入**访问 TodoListService Web API 以用户身份**。
      - 将“状态”始终设置为“已启用”   。
      - 选择“添加作用域”。 

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果守护程序应用调用 web API

在本部分中，将了解如何注册受保护的 web API，使其可以安全地调用通过守护程序应用。

- 你将需要公开*应用程序权限*。 由于守护程序应用不与程序交互的用户，因此委托的权限没有任何意义，将声明仅应用程序权限。
- 租户管理员可以要求 Azure Active Directory (Azure AD) 来颁发令牌的 web API 仅向已注册到一个 web API 的应用程序权限的访问权限的应用程序。

#### <a name="exposing-application-permissions-app-roles"></a>公开应用程序权限 （应用角色）

若要公开应用程序权限，你将需要编辑清单。

1. 在你的应用程序的应用程序注册中，选择**清单**。
1. 通过定位编辑清单`appRoles`设置并添加一个或多个应用程序角色。 以下示例 JSON 块提供了角色定义。 将保留`allowedMemberTypes`设置为`"Application"`仅。 请确保`id`是唯一的 GUID，而`displayName`和`value`不包含空格。
1. 保存清单。

下面的示例显示的内容`appRoles`。 (`id`可以是任何唯一的 GUID。)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>确保 Azure AD 颁发的 web API 只允许客户端令牌

Web API 会检查应用程序角色。 （这是开发人员的方式来公开应用程序权限）。但是，你还可以配置 Azure AD 颁发 web API 的令牌仅对应用批准的租户管理员才能访问你的 API。 若要添加此增强的安全性：

1. 在应用上**概述**应用注册页上，选择包含待你应用程序名称的链接**托管在本地目录中的应用程序**。 此字段的标题可能会被截断。 例如，可能会看到**托管中的应用程序...**

   > [!NOTE]
   >
   > 如果选择此链接，你将转到**企业应用程序概述**与你在其中创建它的租户中的应用程序的服务主体相关联的页。 可以使用浏览器的后退按钮导航回应用注册页。

1. 选择**属性**页面**管理**的企业应用程序页的部分。
1. 如果想要 Azure AD 允许从只有某些客户端访问你的 web API，设置**需要进行用户分配？** 到**是**。

   > [!IMPORTANT]
   >
   > 如果您设置**需要进行用户分配？** 到**是**，请求 web API 的访问令牌时，Azure AD 会检查客户端的应用角色分配。 如果客户端未分配到任何应用程序角色，Azure AD 将返回错误`invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`。
   >
   > 如果将保留**需要进行用户分配？** 设置为**否**，*客户端请求 web API 的访问令牌时，Azure AD 不会检查应用程序角色分配*。 （也就是说，任何客户端使用客户端凭据流） 的任何后台程序客户端将能够为 API 获取访问令牌，只需通过指定它的受众。 任何应用程序将能够访问 API，而无需请求它的权限。 但您的 web API 可以始终上, 一节中所述验证该应用程序具有了正确的角色 （这由租户管理员授权）。 API 通过验证的访问令牌是否具有角色执行此验证声明和此声明的值是否正确。 (在本例中，值是`access_as_application`。)

1. 选择“保存”。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-protected-web-api-app-configuration.md)
