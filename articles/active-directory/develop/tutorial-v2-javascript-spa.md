---
title: JavaScript 单页应用教程 - Microsoft 标识平台 | Azure
description: JavaScript SPA 应用程序如何才能通过 Azure Active Directory v2.0 终结点调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5657a2d2c348b371f81aed74c92e52b5199cdc61
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377409"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>让用户登录并从 JavaScript 单页应用程序 (SPA) 调用 Microsoft 图形 API

本指南演示 JavaScript 单页应用程序 (SPA) 如何执行以下操作：
- 将个人帐户以及工作和学校帐户登录 
- 获取访问令牌
- 从 Microsoft 标识平台终结点调用需要访问令牌的 Microsoft Graph API 或其他 API

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![显示本教程生成的示例应用的工作原理](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>详细信息

本指南创建的示例应用程序允许 JavaScript SPA 查询从 Microsoft 标识平台终结点接受令牌的 Microsoft 图形 API 或 Web API。 在此方案中，用户登录后请求了访问令牌，并通过授权标头将其添加到 HTTP 请求。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|适用于 JavaScript 的 Microsoft 身份验证库预览|

> [!NOTE]
> *Msal.js* 面向 Microsoft 标识平台终结点，个人帐户以及学校和工作帐户可通过该终结点登录并获取令牌。 Microsoft 标识平台终结点有[一些限制](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)。
> 若要了解 v1.0 和 v2.0 终结点之间的差异，请参阅[终结点比较指南](../azuread-dev/azure-ad-endpoint-comparison.md)。

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>设置 Web 服务器或项目

> 想要改为下载此示例的项目？ 执行下列任一操作：
> 
> - 若要使用本地 Web 服务器（如 Node.js）运行项目，请[下载项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。
>
> - （可选）若要使用 Microsoft Internet Information Services (IIS) 服务器运行项目，请[下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。
>
> 若要在执行代码示例之前对其进行配置，请跳到[配置步骤](#register-your-application)。

## <a name="prerequisites"></a>必备条件

* 若要运行本教程，需要将本地 Web 服务器（如 [Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core) 或 IIS Express）与 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 集成。

* 如果使用 Node.js 运行项目，请安装集成开发环境 (IDE)（如 [Visual Studio Code](https://code.visualstudio.com/download)）以编辑项目文件。

* 本指南中的说明基于 Node.js 和 Visual Studio 2017，但你可以使用任何其他开发环境或 Web 服务器。

## <a name="create-your-project"></a>创建项目

> ### <a name="option-1-nodejs-or-other-web-servers"></a>选项1： node.js 或其他 web 服务器
> 确保已安装 [Node.js](https://nodejs.org/en/download/)，然后创建一个用于托管应用程序的文件夹。
>
> ### <a name="option-2-visual-studio"></a>选项 2：Visual Studio
> 如果使用 Visual Studio 并要创建新项目，请执行以下步骤：
> 1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。
> 1. 在 Visual C#\Web 下，选择“ASP.NET Web 应用程序(.NET Framework)”。
> 1. 输入应用程序的名称并选择“确定”。
> 1. 在“新建 ASP.NET Web 应用程序”下，选择“空”。

## <a name="create-the-spa-ui"></a>创建 SPA UI
1. 为 JavaScript SPA 创建 *index.html* 文件。 如果使用 Visual Studio，请选择项目（项目根文件夹）。 右键单击并选择“添加” **“新建项”** “HTML 页”，并将文件命名为  > index.html **。**  > 

1. 在 *index.html* 文件中，添加以下代码：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 可以将上述脚本中的 MSAL.js 版本替换为 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)下的最新发布版本。

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft 身份验证库 (MSAL) 登录用户

将以下代码添加到 `index.html` 文件的 `<script></script>` 标记中：

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>详细信息

用户首次选择“登录”按钮后，**方法将调用** 以将用户登录。`signIn``loginPopup` 此方法会打开一个包含 Microsoft 标识平台终结点的弹出窗口，以提示并验证用户的凭据。 成功登录后，用户将重定向回到原始的 *index.html* 页。 他们将接收到一个由 `msal.js` 处理的令牌，该令牌包含的信息已缓存。 该令牌称为 ID令牌，并包含有关用户的基本信息，如用户显示名。 如果计划将此令牌提供的数据用于任何目的，则需确保此令牌已由后端服务器验证，以保证将令牌颁发给应用程序的有效用户。

本指南生成的 SPA 调用 `acquireTokenSilent` 和/或 `acquireTokenPopup` 来获取用于查询 Microsoft Graph API 以获取用户配置文件信息的访问令牌。 如果需要用于验证 ID 令牌的示例，请查看 GitHub 中的[此](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 示例")示例应用程序。 该示例使用 ASP.NET Web API 进行令牌验证。

#### <a name="getting-a-user-token-interactively"></a>以交互方式获取用户令牌

首次登录后，你不希望在每次用户需要请求令牌来访问资源时，都要求他们重新进行身份验证。 因此，在大部分时间应使用 *acquireTokenSilent* 来获取令牌。 但在某些情况下，需要强制用户与 Microsoft 标识平台终结点交互。 示例包括：

- 由于密码已过期，用户可能需要重新输入其凭据。
- 应用程序正在请求访问资源，这需要用户的许可。
- 需要双重身份验证。

调用 *acquireTokenPopup* 会打开一个弹出窗口（或者，*acquireTokenRedirect* 会将用户重定向到 Microsoft 标识平台终结点）。 在该窗口中，为了进行交互，用户需要确认其凭据、为所需的资源提供许可，或者完成双重身份验证。

#### <a name="getting-a-user-token-silently"></a>以静默方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginPopup`（或 `loginRedirect`）后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用。 （请求或续订令牌的调用是以静默方式发出的。）`acquireTokenSilent` 在某些情况下可能会失败。 例如，用户的密码可能已过期。 应用程序可以通过两种方式处理此异常：

1. 立即调用 `acquireTokenPopup`，这会触发用户登录提示。 此模式通常用于联机应用程序，此时应用程序中没有可供用户使用的未经身份验证的内容。 本指导式设置生成的示例使用此模式。

2. 应用程序还可以直观地提示用户以交互方式登录，用户可以选择在合适的时间登录，或者应用程序可以稍后重试 `acquireTokenSilent`。 如果用户可以在不中断应用程序的情况下使用应用程序的其他功能，则通常会使用此方法。 例如，应用程序中有可用的未经身份验证的内容。 在这种情况下，用户可以决定何时登录并访问受保护的资源，或何时刷新已过时的信息。

> [!NOTE]
> 如果使用的浏览器是 Internet Explorer，本快速入门将使用 `loginRedirect` 和 `acquireTokenRedirect` 方法。 之所以遵循这种做法，是因为 Internet Explorer 处理弹出窗口的方式存在一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>使用刚刚获取的令牌调用 Microsoft Graph API

将以下代码添加到 `index.html` 文件的 `<script></script>` 标记中：

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在本指南创建的示例应用程序中，将使用 `callMSGraph()` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求。 然后，该请求将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 本指南创建的示例应用程序中的资源是 Microsoft Graph API *me* 终结点，它显示用户个人资料信息。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>添加方法以注销用户

将以下代码添加到 `index.html` 文件的 `<script></script>` 标记中：

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>注册应用程序

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 如果帐户提供访问多个租户的权限，请在右上方选择该帐户，然后将门户会话设置为要使用的 Azure AD 租户。
1. 转到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. “注册应用程序”页显示后，请输入应用程序的名称。
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
1. 在“重定向 URI”部分的下拉列表中选择“Web”平台，然后将值设置为基于 Web 服务器的应用程序 URL。

   有关如何设置和获取 Node.js 及 Visual studio 的重定向 URL 的信息，请参阅下面的“设置 Node.js 的重定向 URL”部分和[设置 Visual Studio 的重定向 URL](#set-a-redirect-url-for-visual-studio)。

1. 选择“注册”。
1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用。
1. 本快速入门要求启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)。 在已注册的应用程序的左窗格中，选择“身份验证”。
1. 在“高级设置”部分的“隐式授权”下，选中“ID 令牌”和“访问令牌”复选框。 由于此应用必须将用户登录并调用 API，因此需要 ID 令牌和访问令牌。
1. 选择“保存”。

> #### <a name="set-a-redirect-url-for-nodejs"></a>设置 Node.js 的重定向 URL
> 对于 Node.js，可以在 *server.js* 文件中设置 Web 服务器端口。 本教程使用端口 30662，但你可以使用任何其他可用端口。
>
> 若要设置应用程序注册信息中的重定向 URL，请切换回“应用程序注册”窗格，然后执行以下两项操作之一：
>
> - 将 *`http://localhost:30662/`* 设置为“重定向 URL”。
> - 如果使用的是自定义 TCP 端口，请使用 *`http://localhost:<port>/`* （其中， *\<端口>* 是自定义 TCP 端口号）。
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>设置 Visual Studio 的重定向 URL
> 若要获取 Visual Studio 的重定向 URL，请执行以下步骤：
> 1. 在解决方案资源管理器中选择项目。
>
>    此时将打开“属性”窗口。 如果未打开，请按 F4。
>
>    ![JavaScriptSPA 项目属性窗口](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. 复制“URL”的值。
> 1. 切换回“应用程序注册”窗格，然后将已复制的值粘贴为“重定向 URL”。

#### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

1. 向项目设置期间创建的 *index.html* 文件中添加应用程序注册信息。 在文件顶部的 `<script></script>` 标记中添加以下代码：

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    其中：
    - *\<Enter_the_Application_Id_here>* 是已注册的应用程序的“应用程序(客户端) ID”。
    - *\<Enter_the_Tenant_info_here>* 设置为以下选项之一：
       - 如果应用程序支持“此组织目录中的帐户”，请将此值替换为“租户 ID”或“租户名称”（例如，*contoso.microsoft.com*）。
       - 如果应用程序支持“任何组织目录中的帐户”，请将此值替换为 **organizations**。
       - 如果应用程序支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为 **common**。 若要将支持限于“仅限个人 Microsoft 帐户”，请将此值替换为 **consumers**。

## <a name="test-your-code"></a>测试代码

使用以下环境之一测试代码。

### <a name="test-with-nodejs"></a>使用 Node.js 进行测试

如果使用的不是 Visual Studio，请确保 Web 服务器已启动。

1. 配置服务器侦听基于“index.html”文件位置的 TCP 端口。 对于 Node.js，请通过在命令提示符下从应用程序文件夹运行以下命令，启动 Web 服务器来侦听该端口：

    ```bash
    npm install
    node server.js
    ```
1. 在浏览器中，输入 **http://\<span>\</span>localhost:30662** 或 **http://\<span>\</span>localhost:{port}** ，其中 *port* 是 Web 服务器正在侦听的端口。 应会显示 index.html 文件的内容和“登录”按钮。

### <a name="test-with-visual-studio"></a>使用 Visual Studio 进行测试

如果使用的是 Visual Studio，请选择项目解决方案，然后按 F5 运行项目。 浏览器将打开到 http://<span></span>localhost:{port} 位置，然后应显示“登录”按钮。

## <a name="test-your-application"></a>测试应用程序

在浏览器加载 index.html 文件后，选择“登录”。 系统将提示你使用 Microsoft 标识平台终结点进行登录：

![JavaScript SPA 帐户登录窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次登录到应用程序时，系统会提示你授予其访问你的个人资料的权限，并将你登录：

![“请求的权限”窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>查看应用程序结果

登录后，你的用户个人资料信息将在显示的 Microsoft Graph API 响应中返回：

![Microsoft Graph API 调用的结果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，在注册门户上注册的每个应用程序中，都会自动添加此范围。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要使用 *Calendars.Read* 范围列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 *Calendars.Read* 委派权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

如果后端 API 不需要范围（不建议），则你可以将 *clientId* 用作调用中的范围来获取令牌。

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

帮助我们改进 Microsoft 标识平台。 通过完成简短的两问题调查，告诉我们你的想法。

> [!div class="nextstepaction"]
> [Microsoft 标识平台调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
