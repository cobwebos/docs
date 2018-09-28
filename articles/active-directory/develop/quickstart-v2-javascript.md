---
title: Azure AD v2 JavaScript 快速入门 | Microsoft Docs
description: 了解 JavaScript 应用程序如何才能通过 Azure Active Directory v2.0 终结点调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4c64552ab23331755bf1d292bede61e78b339df0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987418"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>快速入门：通过 JavaScript 应用程序登录用户并获取访问令牌

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

本快速入门介绍如何使用代码示例来演示 JavaScript 单页应用程序 (SPA) 如何登录个人、工作和学校帐户，获取访问令牌以及调用 Microsoft Graph API。

![本快速入门生成的示例应用的工作原理](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>注册应用程序并下载快速入门应用
>
> ### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序。
> 1. 在“应用程序名称”框中输入应用程序的名称。
> 1. 确保未选中“指导式设置”复选框，然后选择“创建”。
> 1. 单击“添加平台”，然后选择“Web”。
> 1. 确保“允许隐式流”保持选中状态。
> 1. 在“重定向 URL”下添加 `http://localhost:30662/`。
> 1. 单击“ **保存**”。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使本快速入门的代码示例正常运行，需将重定向 URI 添加为 `http://localhost:30662/` 并启用“隐式授权”。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-javascript/green-check.png) 应用程序已使用这些属性进行配置

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

可以根据开发环境选择这些选项之一。
* [下载核心项目文件 - 针对 Web 服务器，例如 Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)
* [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)

将 zip 文件提取到本地文件夹（例如，C:\Azure-Samples）。

#### <a name="step-3-configure-your-javascript-app"></a>步骤 3：配置 JavaScript 应用

> [!div renderon="docs"]
> 编辑 `msalconfig.js`，将 `Enter_the_Application_Id_here` 替换为刚注册应用的应用程序 ID。 可以在“概览”页上找到“应用程序 ID”。

> [!div class="sxs-lookup" renderon="portal"]
> 编辑 `msalconfig.js`，将 msalconfig 替换为：

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
> [!NOTE]
> 如果使用 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)，则重定向 URI 会被设置为 `http://localhost:30662/`，就像在代码示例的项目中配置一样。 如果使用 [Node.js](https://nodejs.org/en/download/) 或任何其他的 Web 服务器，请将重定向 URI 设置为 `http://localhost:30662/`，然后将服务器配置为开始在此端口上侦听。
>

#### <a name="step-4-run-the-project"></a>步骤 4：运行项目

如果使用 Visual Studio，请按 F5 运行项目。

如果使用 Node.js，则可在命令行上通过项目的目录运行以下命令，以便启动服务器：
 ```batch
 npm install
 node server.js
 ```
打开 Web 浏览器并导航到 `http://localhost:30662/`。 单击“调用 Microsoft Graph API”按钮，启动登录。


## <a name="more-information"></a>更多信息

### <a name="msaljs"></a>*msal.js*

MSAL 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft Azure Active Directory (Azure AD) 保护的 API。 本快速入门的 *index.html* 包含对库的引用：

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
````

或者，如果已安装 Node，则可通过 npm 下载它：

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL 初始化

快速入门代码也演示了如何初始化库：

```javascript
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});
```

> |其中  |  |
> |---------|---------|
> |`ClientId`     |Azure 门户中注册的应用程序的应用程序 ID|
> |`authority`    |它是颁发机构 URL。 传递 *null* 会将默认颁发机构设置为 `https://login.microsoftonline.com/common`。 如果应用为单租户（只以一个目录中的帐户为目标），请将此值设置为 `https://login.microsoftonline.com/<tenant name or ID>`|
> |`loginCallBack`| 在身份验证之后调用的回调方法会重定向回应用|
> |`redirectUri`  |一个 URL，在通过 Azure AD 进行身份验证之后，用户会重定向回此 URL|

### <a name="sign-in-users"></a>用户登录

以下代码片段演示如何进行用户登录：

```javascript
userAgentApplication.loginRedirect(graphAPIScopes);
```

> |其中  |  |
> |---------|---------|
> | `graphAPIScopes`   | （可选）包含在登录时为了获得用户同意而请求的作用域（例如：`[ "user.read" ]` 适用于 Microsoft Graph，`[ "api://<Application ID>/access_as_user" ]` 适用于自定义 Web API）。 |

> [!TIP]
> 或者，可能需要使用 `loginPopup` 方法来显示一个弹出窗口，方便用户登录。

### <a name="request-tokens"></a>请求令牌

MSAL 有三种用于获取令牌的方法，即 `acquireTokenRedirect`、`acquireTokenPopup`、`acquireTokenSilent`：

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginRedirect` 或 `loginPopup` 方法后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用。 进行请求或续订令牌的调用时，以静默方式进行。

```javascript
// Try to acquire the token used to query Graph API silently first:
userAgentApplication.acquireTokenSilent(graphAPIScopes)
```

> |其中  |  |
> |---------|---------|
> | `graphAPIScopes`   | 包含请求的需要在 API 的访问令牌中返回的作用域（例如：`[ "user.read" ]` 适用于 Microsoft Graph，`[ "api://<Application ID>/access_as_user" ]` 适用于自定义 Web API）。 |

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

 有些情况下，需强制用户与 Azure AD v2.0 终结点交互。 例如：
* 由于密码已过期，用户可能需要重新输入凭据
* 应用程序正在请求访问用户需要同意的其他资源作用域
* 需要双重身份验证

建议用于大多数应用程序的常用模式是先调用 `acquireTokenSilent`，然后捕获异常，然后再调用 `acquireTokenRedirect`（或 `acquireTokenPopup`），以便启动交互式请求。

调用 `acquireTokenRedirect(scope)` 导致将用户重定向到 Azure AD v2.0 终结点（调用 `acquireTokenPopup(scope)` 则会导致出现一个弹出窗口），在这种情况下，用户需要进行交互，即确认凭证、同意所需资源或完成双重身份验证。

```javascript
userAgentApplication.acquireTokenRedirect(graphAPIScopes);
```

## <a name="next-steps"></a>后续步骤

如需更详细的分步指南，以便了解如何生成适用于本快速入门的应用程序，请尝试下面的 JavaScript 教程。

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>了解为本快速入门创建应用程序的步骤

> [!div class="nextstepaction"]
> [调用 Graph API 教程](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>浏览 MSAL 存储库中的文档、常见问题解答、问题等

> [!div class="nextstepaction"]
> [msal.js GitHub 存储库](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
