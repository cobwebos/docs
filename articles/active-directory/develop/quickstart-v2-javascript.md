---
title: Azure AD v2 JavaScript 快速入门 | Microsoft Docs
description: 了解 JavaScript 应用程序如何才能通过 Azure Active Directory v2.0 终结点调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 69c77896f894201d1419aaef33470a02ac45ff91
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986282"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>快速入门：通过 JavaScript 应用程序登录用户并获取访问令牌

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

在本快速入门中，你将了解如何使用代码示例，该示例演示了 JavaScript 单页应用程序 (SPA) 登录个人、工作和学校帐户并获取访问令牌以调用 Microsoft Graph API 或任何 Web API 的方式。

![本快速入门生成的示例应用如何工作](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>注册应用程序并下载快速入门应用
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 登录到 [Azure 门户](https://portal.azure.com/)以注册应用程序。
> 1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
> 1. 在左侧导航窗格中选择“Azure Active Directory”服务，然后选择“应用注册(预览版)”>“新建注册”。
> 1. “注册应用程序”页显示后，请输入应用程序的名称。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
> 1. 选择“重定向 URI”部分下的“Web”平台，并将值设置为 `http://localhost:30662/`。
> 1. 完成后，选择“注册”。  在应用的“概述”页上，记下“应用程序(客户端) ID”值。
> 1. 本快速入门要求启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)。 在已注册的应用程序的左侧导航窗格中，选择“身份验证”。
> 1. 在“高级设置”中的“隐式授权”下，同时勾选“ID 令牌”和“访问令牌”复选框。 由于此应用需要将用户登录并调用 API，因此需要 ID 令牌和访问令牌。
> 1. 选择“保存”。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使本快速入门的代码示例正常运行，需将重定向 URI 添加为 `http://localhost:30662/` 并启用“隐式授权”。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-javascript/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

可以根据开发环境选择这些选项之一。
* [下载核心项目文件 - 针对 Web 服务器，例如 Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

将 zip 文件提取到本地文件夹（例如，**C:\Azure-Samples**）。

#### <a name="step-3-configure-your-javascript-app"></a>步骤 3：配置 JavaScript 应用

> [!div renderon="docs"]
> 编辑 `index.html` 并将 `applicationConfig` 下的 `Enter_the_Application_Id_here` 替换为你刚才注册的应用的应用程序 ID。

> [!div class="sxs-lookup" renderon="portal"]
> 编辑 `index.html` 并将 `applicationConfig` 替换为：

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>如果使用 [Node.js](https://nodejs.org/en/download/)，则需要配置 *server.js* 文件，以便服务器开始在端口 30662 上进行侦听。
> 如果使用 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)，则需要配置代码示例的 *.csproj* 文件，以便服务器开始在端口 30662 上进行侦听。
>

#### <a name="step-4-run-the-project"></a>步骤 4：运行项目

* 如果使用 Node.js：

    1. 通过项目的目录运行以下命令，以便启动服务器：

        ```batch
        npm install
        node server.js
        ```

    1. 打开 Web 浏览器并导航到 `http://localhost:30662/`。
    1. 单击“登录”按钮以开始登录，然后调用 Microsoft Graph API。


* 如果使用 Visual Studio，请确保选择项目解决方案，然后按 **F5** 来运行项目。

## <a name="more-information"></a>更多信息

### <a name="msaljs"></a>*msal.js*

MSAL 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft Azure Active Directory (Azure AD) 保护的 API。 本快速入门的 *index.html* 包含对库的引用：

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

或者，如果已安装 Node，则可通过 npm 下载它：

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL 初始化

快速入门代码也演示了如何初始化库：

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |其中  |  |
> |---------|---------|
> |`ClientId`     |Azure 门户中注册的应用程序的应用程序 ID|
> |`authority`    |它是颁发机构 URL。 传递 *null* 会将默认颁发机构设置为 `https://login.microsoftonline.com/common`。 如果应用为单租户（只以一个目录中的帐户为目标），请将此值设置为 `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| 在身份验证之后调用的回调方法会重定向回应用。 此处传递了 `acquireTokenRedirectCallBack`。 如果使用 loginPopup，则此项为空。|
> |`options`  |可选参数的集合。 在本例中，`storeAuthStateInCookie` 和 `cacheLocation` 是可选配置。 有关选项的更多详细信息，请参阅 [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options)。 |

### <a name="sign-in-users"></a>用户登录

以下代码片段演示如何进行用户登录：

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |其中  |  |
> |---------|---------|
> | `scopes`   | （可选）包含在登录时为了获得用户同意而请求的作用域。 例如：`[ "user.read" ]` 适用于 Microsoft Graph，`[ "<Application ID URL>/scope" ]` 适用于自定义 Web API（即 `api://<Application ID>/access_as_user`）。 此处传递了 `applicationConfig.graphScopes`。 |

> [!TIP]
> 另外，你可能希望使用 `loginRedirect` 方法来将当前页面重定向到登录页面而非重定向到弹出窗口。

### <a name="request-tokens"></a>请求令牌

MSAL 有三种用于获取令牌的方法，即 `acquireTokenRedirect`、`acquireTokenPopup`、`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginRedirect` 或 `loginPopup` 方法后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用。 进行请求或续订令牌的调用时，以静默方式进行。

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |其中  |  |
> |---------|---------|
> | `scopes`   | 包含请求的需要在 API 的访问令牌中返回的作用域。 例如：`[ "user.read" ]` 适用于 Microsoft Graph，`[ "<Application ID URL>/scope" ]` 适用于自定义 Web API（即 `api://<Application ID>/access_as_user`）。 此处传递了 `applicationConfig.graphScopes`。|

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

有些情况下，需强制用户与 Azure AD v2.0 终结点交互。 例如：
* 由于密码已过期，用户可能需要重新输入凭据
* 应用程序正在请求访问用户需要同意的其他资源作用域
* 需要双重身份验证

建议用于大多数应用程序的常用模式是先调用 `acquireTokenSilent`，然后捕获异常，然后再调用 `acquireTokenRedirect`（或 `acquireTokenPopup`），以便启动交互式请求。

调用 `acquireTokenPopup(scope)` 将导致显示一个用于登录的弹出窗口（调用 `acquireTokenRedirect(scope)` 则会导致将用户重定向到 Azure AD v2.0 终结点），在这种情况下，用户需要进行交互，即确认凭证、同意所需资源或完成双重身份验证。

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> 由于存在与 Internet Explorer 浏览器处理弹出窗口有关的[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)，当使用的浏览器是 Internet Explorer 时，本快速入门使用 `loginRedirect` 和 `acquireTokenRedirect` 方法。

## <a name="next-steps"></a>后续步骤

如需更详细的分步指南，以便了解如何生成适用于本快速入门的应用程序，请尝试下面的 JavaScript 教程。

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>了解为本快速入门创建应用程序的步骤

> [!div class="nextstepaction"]
> [调用 Graph API 教程](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>浏览 MSAL 存储库中的文档、常见问题解答、问题等

> [!div class="nextstepaction"]
> [msal.js GitHub 存储库](https://github.com/AzureAD/microsoft-authentication-library-for-js)
