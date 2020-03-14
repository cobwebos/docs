---
title: 受保护的 web API 应用注册 |Microsoft
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 2d9e5d051f101288b8528b47fa88b4783a040950
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262498"
---
# <a name="protected-web-api-app-registration"></a>受保护的 web API：应用注册

本文介绍了受保护的 web API 的应用注册的具体信息。

有关注册应用程序的常见步骤，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。

## <a name="accepted-token-version"></a>接受的令牌版本

Microsoft 标识平台终结点可以颁发1.0 版令牌和 v2.0 令牌。 有关这些令牌的详细信息，请参阅[访问令牌](access-tokens.md)。

接受的令牌版本取决于创建应用程序时所选择的**受支持的帐户类型**值。

- 如果**支持的帐户类型**的值是**任何组织目录中的帐户和个人 Microsoft 帐户（例如 Skype、Xbox、Outlook.com）** ，则接受的令牌版本为 v2.0。
- 否则，接受的令牌版本为1.0。

创建应用程序后，可以通过执行以下步骤来确定或更改接受的令牌版本：

1. 在 Azure 门户中，选择你的应用，然后选择 "**清单**"。
1. 找到清单中的属性**accessTokenAcceptedVersion** 。 此属性的默认值为2。
1. 该值指定 web API 接受的令牌版本 Azure Active Directory （Azure AD）。
    - 如果值为2，则 web API 接受 v2.0 令牌。
    - 如果该值为**null**，则 web API 接受 v2.0 1.0 令牌。
1. 如果更改了令牌版本，请选择 "**保存**"。

> [!NOTE]
> Web API 指定它接受的令牌版本。 当客户端从 Microsoft 标识平台（v2.0）终结点请求你的 web API 的令牌时，客户端将获取一个令牌，指示 web API 接受哪个令牌版本。

## <a name="no-redirect-uri"></a>无重定向 URI

Web Api 不需要注册重定向 URI，因为没有用户以交互方式登录。

## <a name="exposed-api"></a>公开的 API

Web Api 特定的其他设置是公开的 API 和公开的作用域。

### <a name="application-id-uri-and-scopes"></a>应用程序 ID URI 和作用域

作用域的格式通常 `resourceURI/scopeName`。 对于 Microsoft Graph，范围具有快捷方式。 例如，`User.Read` 是 `https://graph.microsoft.com/user.read`的快捷方式。

在应用程序注册过程中，需要定义以下参数：

- 资源 URI
- 一个或多个作用域
- 一个或多个应用角色

默认情况下，应用程序注册门户建议使用资源 URI `api://{clientId}`。 此 URI 是唯一的，但不是可读的。 如果更改 URI，请确保新值是唯一的。

对于客户端应用程序，范围显示为*委托权限*，应用角色显示为 web API 的*应用程序权限*。

作用域也会显示在向应用用户显示的许可窗口上。 因此，您需要提供描述范围的相应字符串：

- 如用户所见。
- 如租户管理员所示，可以授予管理员同意。

### <a name="exposing-delegated-permissions-scopes"></a>公开委托权限（范围）

1. 选择 "在应用程序注册中**公开 API** "。
1. 选择“添加范围”。
1. 如果系统提示，请选择 "**保存并继续**"，接受建议的应用程序 ID URI （`api://{clientId}`）。
1. 指定以下值：
    - 选择 "**作用域名称**"，然后输入**access_as_user**。
    - 选择**可以同意的人员**，并确保选择 "**管理员" 和 "用户**"。
    - 选择 "**管理员许可显示名称**"，并**以用户身份输入 Access TodoListService**。
    - 选择**管理员同意说明**，并**按用户身份访问 TodoListService Web API**。
    - 选择 "**用户同意显示名称**"，并**以用户身份输入 "访问 TodoListService**"。
    - 选择**用户同意说明**，并**按用户身份访问 TodoListService Web API**。
    - 将 "**状态**" 值设置为 "**已启用**"。
 1. 选择 "**添加作用域**"。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果 web API 由守护程序应用调用

在本部分中，将了解如何注册受保护的 web API，以便后台程序应用能够安全地调用它。

- 只声明并公开*应用程序权限*，因为后台程序应用不与用户交互。 委托的权限并不合理。
- 租户管理员可以要求 Azure AD 仅向已注册为访问某个 API 应用程序权限的应用程序发出 web API 令牌。

#### <a name="exposing-application-permissions-app-roles"></a>公开应用程序权限（应用程序角色）

若要公开应用程序权限，需要编辑清单。

1. 在应用程序的应用程序注册中，选择 "**清单**"。
1. 若要编辑清单，请查找 `appRoles` 设置并添加应用程序角色。 以下示例 JSON 块提供了角色定义。
1. 将 `allowedMemberTypes` 设置为 "仅 `"Application"`"。
1. 请确保 `id` 是唯一的 GUID。
1. 请确保 `displayName` 和 `value` 不包含空格。
1. 保存清单。

下面的示例演示 `appRoles`的内容，其中 `id` 的值可以是任何唯一的 GUID。

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

Web API 检查应用角色。 此角色是软件开发人员提供应用程序权限的一种方法。 你还可以将 Azure AD 配置为仅向租户管理员批准 API 访问的应用颁发 API 令牌。

添加此增强的安全性：

1. 请参阅应用程序**概述**页获取应用注册。
1. 在 "**本地目录中的托管应用程序**" 下，选择包含你的应用程序名称的链接。 此选择的标签可能会被截断。 例如，你可能会**在中看到托管应用程序**。

   > [!NOTE]
   >
   > 选择此链接时，将打开 "**企业应用程序概述**" 页。 此页面与你在其中创建应用程序的租户中的应用程序的服务主体相关联。 可以通过使用浏览器的 "后退" 按钮来切换到 "应用注册" 页。

1. 选择 "企业应用程序" 页的 "**管理**" 部分中的 "**属性**" 页。
1. 如果希望 Azure AD 仅允许从特定客户端访问 web API，请将 **"需要进行用户分配"** 设置为 **"是"** 。

   > [!IMPORTANT]
   >
   > 如果将 "**用户分配"** 设置为 **"是"** ，则在客户端请求 web API 访问令牌时，Azure AD 会检查客户端的应用角色分配。 如果客户端未分配到任何应用角色，Azure AD 将返回错误消息 "invalid_client： AADSTS501051：应用程序 \<应用程序名称\> 未分配给 \<web API\>的角色。
   >
   > 如果你将 **"用户分配"** 设置为 "**否**"，则在客户端请求你的 web API 的访问令牌时，Azure AD 不会检查应用角色分配。 任何使用客户端凭据流的客户端（即任何使用客户端凭据流的客户端）都可以通过指定其受众来获取 API 的访问令牌。 任何应用程序都可以访问 API，而无需为其请求权限。
   >
   > 但如前一部分中所述，你的 web API 始终可以验证应用程序是否具有适当的角色，该角色由租户管理员授权。该 API 通过验证访问令牌是否具有角色声明以及此声明的值是否正确来执行此验证。 在前面的 JSON 示例中，值是 `access_as_application`。

1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用代码配置](scenario-protected-web-api-app-configuration.md)
