---
title: 受保护的 Web API - 应用注册 | Azure
description: 了解如何构建受保护的 web API 和注册应用所需的信息。
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
ms.openlocfilehash: bbccfc38a4e5e4b31cb625c614e838a3c92e7429
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562307"
---
# <a name="protected-web-api-app-registration"></a>受保护的 web API:应用注册

本文介绍了受保护的 web API 的应用注册的具体信息。

请参阅[快速入门：向 Microsoft 标识平台](quickstart-register-app.md)注册应用程序, 以获取注册应用程序的常见步骤。

## <a name="accepted-token-version"></a>接受的令牌版本

Microsoft 标识平台终结点可以发出两种类型的令牌：v1.0 令牌和 v2.0 令牌。 有关这些令牌的详细信息, 请参阅[访问令牌](access-tokens.md)。 接受的令牌版本取决于创建应用程序时选择的**支持的帐户类型**:

- 如果**支持的帐户类型**的值是**任何组织目录中的帐户和个人 Microsoft 帐户 (例如 Skype、Xbox、Outlook.com)** , 则接受的令牌版本将为 v2.0。
- 否则, 接受的令牌版本将为1.0 版。

创建应用程序后, 可以通过执行以下步骤来确定或更改接受的令牌版本:

1. 在 Azure 门户中选择应用，然后选择应用的“清单”。
2. 在清单中搜索 **"accessTokenAcceptedVersion"** 。 请注意, 其值为**2**。 此属性指定 web API 接受 v2.0 令牌的 Azure Active Directory (Azure AD)。 如果该值为**null**, 则接受的令牌版本为1.0。
3. 如果更改了令牌版本, 请选择 "**保存**"。

> [!NOTE]
> Web API 指定它接受的令牌版本 (1.0 或 v2.0)。 当客户端从 Microsoft 标识平台 (v2.0) 终结点请求你的 web API 的令牌时, 它们将获取一个令牌, 指示 web API 接受哪个版本。

## <a name="no-redirect-uri"></a>无重定向 URI

Web Api 不需要注册重定向 URI, 因为没有用户以交互方式登录。

## <a name="expose-an-api"></a>公开 API

特定于 Web API 的另一设置是公开的 API 和公开的作用域

### <a name="resource-uri-and-scopes"></a>资源 URI 和作用域

作用域的格式`resourceURI/scopeName`通常为。 对于 Microsoft Graph, 范围具有类似`User.Read`的快捷方式。 此字符串是的快捷方式`https://graph.microsoft.com/user.read`。

在应用程序注册过程中, 需要定义以下参数:

- 资源 URI。 默认情况下, 应用程序注册门户建议使用`api://{clientId}`。 此资源 URI 独一无二，但不是人工可读的。 您可以更改它, 但请确保新值是唯一的。
- 一个或多个*范围*。 (对于客户端应用程序, 它们将显示为你的 web API 的*委托权限*。)
- 一个或多个*应用角色*。 (对于客户端应用程序, 它们将显示为你的 web API 的*应用程序权限*。)

范围还会显示在 "同意" 屏幕上, 显示给你的应用程序的最终用户。 因此, 您需要提供描述该范围的相应字符串:

- 由最终用户查看。
- 如租户管理员所见, 可以授予管理员同意。

### <a name="exposing-delegated-permissions-scopes"></a>公开委托权限 (范围)

1. 在应用程序注册中选择 "**公开 API** " 部分。
1. 选择“添加范围”。
1. 如果系统提示, 请通过选择 "`api://{clientId}`**保存并继续**" 来接受建议的应用程序 ID URI ()。
1. 输入以下参数:
      - 对于 "**作用域名称**", 请使用**access_as_user**。
      - 对于**可以同意的用户**, 请确保选择 "**管理员" 和 "用户**"。
      - 在 "**管理员同意显示名称**" 中, 输入**作为用户的 "访问 TodoListService**"。
      - 在**管理员同意说明**中, 输入**以用户身份访问 TodoListService Web API**。
      - 在 "**用户同意显示名称**" 中, 输入**作为用户的 "访问 TodoListService**"。
      - 在 "**用户同意说明**" 中, 输入**以用户身份访问 TodoListService Web API**。
      - 将“状态”始终设置为“已启用”。
      - 选择“添加作用域”。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果 web API 由守护程序应用调用

在本部分中, 你将了解如何注册受保护的 web API, 以使其可以由守护程序应用安全地调用。

- 你需要公开*应用程序权限*。 你将仅声明应用程序权限, 因为后台应用程序不会与用户交互, 因此委托的权限不会有意义。
- 租户管理员可以要求 Azure Active Directory (Azure AD) 仅向已注册为访问某个 web API 的应用程序权限的应用程序颁发 web API 的令牌。

#### <a name="exposing-application-permissions-app-roles"></a>公开应用程序权限 (应用程序角色)

若要公开应用程序权限, 需要编辑清单。

1. 在应用程序的应用程序注册中, 选择 "**清单**"。
1. 通过查找`appRoles`设置并添加一个或多个应用程序角色来编辑清单。 以下示例 JSON 块提供了角色定义。 将设置`allowedMemberTypes`为`"Application"` "仅"。 请确保`id`是唯一`displayName`的 GUID, 并且不包含空格。`value`
1. 保存清单。

下面的示例显示了的`appRoles`内容。 `id` (可以是任何唯一的 GUID。)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>确保 Azure AD 仅向允许的客户端颁发 web API 的令牌

Web API 检查应用角色。 (这是公开应用程序权限的开发人员方法。)但你也可以将 Azure AD 配置为仅向租户管理员批准的应用程序授予访问 API 所批准的应用程序的令牌。 添加此增强的安全性:

1. 在应用注册的 "应用**概述**" 页上, 在 "**本地目录" 中的 "托管应用程序**" 下选择包含应用名称的链接。 此字段的标题可能会被截断。 例如, 你可能会看到**中的托管应用程序**。

   > [!NOTE]
   >
   > 选择此链接后, 你将会看到与你在其中创建应用程序的租户中的应用程序的服务主体关联的**企业应用程序概述**页。 可以通过使用浏览器的 "后退" 按钮, 导航回应用程序注册页。

1. 选择 "企业应用程序" 页的 "**管理**" 部分中的 "**属性**" 页。
1. 如果希望 Azure AD 仅允许从特定客户端访问 web API, 请将 **"需要进行用户分配"** 设置为 **"是"** 。

   > [!IMPORTANT]
   >
   > 如果将 "**用户分配"** 设置为 **"是"** , Azure AD 会在客户端请求访问 web API 的访问令牌时检查其应用程序角色分配。 如果客户端未分配到任何应用角色, Azure AD 将返回错误`invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`。
   >
   > 如果你将 **"用户分配"** 设置为 "**否**", 则*在客户端请求你的 web API 的访问令牌时, Azure AD 不会检查应用角色分配*。 任何守护程序客户端 (即任何使用客户端凭据流的客户端) 都能够仅通过指定其受众来获取 API 的访问令牌。 任何应用程序都可以访问 API, 而无需为其请求权限。 但你可以始终根据上一节所述, 验证应用程序是否具有适当的角色 (由租户管理员授权)。 该 API 通过验证访问令牌是否具有角色声明以及此声明的值是否正确来执行此验证。 (在我们的示例中, 该值`access_as_application`为。)

1. 选择**保存**。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-protected-web-api-app-configuration.md)
