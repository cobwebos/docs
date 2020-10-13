---
title: 教程：创建使用身份验证代码流的 JavaScript 单页应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将创建一个 JavaScript SPA，它可以使用户登录，并使用身份验证代码流从 Microsoft 标识平台获取访问令牌，并调用 Microsoft Graph API。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3caf12e13b5999c40843f1203ac8ce7f2f21ef6b
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665865"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>教程：使用授权代码流让用户登录并从 JavaScript 单页应用 (SPA) 调用 Microsoft Graph API

本教程演示如何创建一个 JavaScript 单页应用程序 (SPA)，它使用适用于 JavaScript v2.0 的 Microsoft 身份验证库 (MSAL) 执行以下操作：

> [!div class="checklist"]
> * 通过 PKCE 执行 OAuth 2.0 授权代码流
> * 将个人 Microsoft 帐户以及工作和学校帐户登录
> * 获取访问令牌
> * 调用需要从 Microsoft 标识平台终结点获取的访问令牌的 Microsoft Graph 或你自己的 API

MSAL.js 2.0 支持浏览器中的授权代码流（而不是隐式授权流），从而在 MSAL.js 1.0 的基础上进行了改进。 MSAL.js 2.0 不支持隐式流。

[!INCLUDE [MSAL.js 2.0 and Azure AD B2C temporary incompatibility notice](../../../includes/msal-b2c-cors-compatibility-notice.md)]

## <a name="prerequisites"></a>先决条件

* 用于运行本地 Web 服务器的 [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

## <a name="how-the-tutorial-app-works"></a>教程应用的工作方式

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

在本教程中创建的应用程序使 JavaScript SPA 可以通过从 Microsoft 标识平台终结点获取安全令牌来查询 Microsoft Graph API。 此方案中，用户登录后请求了访问令牌，并在授权标头将其添加到 HTTP 请求。 令牌获取和更新由适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 处理。

本教程使用以下库：

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) 适用于 JavaScript v2.0 的 Microsoft 身份验证库浏览器包

## <a name="get-the-completed-code-sample"></a>获取完整代码示例

想要改为下载本教程的完整示例项目？ 若要使用本地 Web 服务器（如 Node.js）运行项目，请克隆 [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) 存储库：

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

然后，若要在执行代码示例之前对其进行配置，请跳到[配置步骤](#register-your-application)。

若要继续学习本教程并自行生成应用程序，请前进到下一节[先决条件](#prerequisites)。

## <a name="create-your-project"></a>创建项目

安装了 [node.js](https://nodejs.org/en/download/) 后，创建一个文件夹来托管应用程序，例如 msal-spa-tutorial。

接下来，实现一个小型 [Express](https://expressjs.com/) Web 服务器来为 index.html 文件提供服务。

1. 首先，在终端中更改到你的项目目录，然后运行以下 `npm` 命令：
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. 接下来，创建名为 server.js 的文件并添加以下代码：

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

现在，已有一个可为 SPA 提供服务的小型 Web 服务器。 完成本教程的其余部分之后，项目的文件和文件夹结构应类似于下面这样：

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>创建 SPA UI

1. 在项目目录中创建 app 文件夹，并在其中为 JavaScript SPA 创建 index.html 文件。 此文件实现通过 Bootstrap 4 Framework 生成的 UI，并导入用于配置、身份验证和 API 调用的脚本文件。

    在 *index.html* 文件中，添加以下代码：

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
          </div>
        </div>
        </div>
        <br>
        <br>
          <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
          </div>
          <div class="col-md-5">
            <div class="tab-content" id="nav-tabContent">
            </div>
          </div>
        </div>
        <br>
        <br>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. 接下来在 app 文件夹中，还创建一个名为 ui.js 的文件并添加以下代码。 此文件将访问和更新 DOM 元素。

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>注册应用程序

按照[单页应用程序：应用注册](scenario-spa-app-registration.md)中的步骤为 SPA 创建应用注册。

在[重定向 URI：具有授权代码流的 MSAL.js 2.0](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) 步骤中，输入 `http://localhost:3000`（本教程的应用程序运行时的默认位置）。

如果要使用其他端口，请输入 `http://localhost:<port>`，其中 `<port>` 是首选 TCP 端口号。 如果指定的端口号不是 `3000`，则还要使用首选端口号更新 server.js。

### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

在 app 文件夹中创建名为 authConfig.js 的文件来包含用于身份验证的配置参数，然后添加以下代码 ：

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

修改 `msalConfig` 部分中的值，如下所述：

- `Enter_the_Application_Id_Here`：已注册应用程序的应用程序（客户端）ID。
- `Enter_the_Cloud_Instance_Id_Here`：在其中注册应用程序的 Azure 云实例。
  - 对于主要（或全球）Azure 云，请输入 `https://login.microsoftonline.com`。
  - 对于国家/地区云（例如中国云），可以在[国家/地区云](authentication-national-cloud.md)中找到相应值。
- `Enter_the_Tenant_info_here` 应是以下各项之一：
  - 如果应用程序支持“此组织目录中的帐户”，请将此值替换为“租户 ID”或“租户名称”。 例如，`contoso.microsoft.com`。
  - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为“`organizations`”。
  - 如果应用支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为“`common`”。
  - 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为“`consumers`”。
- `Enter_the_Redirect_Uri_Here` 为 `http://localhost:3000`。

如果使用的是全球 Azure 云，则 authConfig 中的 `authority` 值应类似于：

```javascript
authority: "https://login.microsoftonline.com/common",
```

在 app 文件夹中，创建名为“graphConfig.js”的文件 。 添加以下代码，以便为应用程序提供用于调用 Microsoft Graph API 的配置参数：

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

修改 `graphConfig` 部分中的值，如下所述：

- `Enter_the_Graph_Endpoint_Here` 是应用程序应与之通信的 Microsoft Graph API 实例。
  - 对于全球 Microsoft Graph API 终结点，请将此字符串的两个实例都替换为 `https://graph.microsoft.com`。
  - 对于国家/地区云部署中的终结点，请参阅 Microsoft Graph 文档中的[国家/地区云部署](https://docs.microsoft.com/graph/deployments)。

如果使用的是全球终结点，则 graphConfig.js 中的 `graphMeEndpoint` 和 `graphMailEndpoint` 值应类似于：

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>使用 Microsoft 身份验证库 (MSAL) 登录用户

### <a name="pop-up"></a>弹出

在 app 文件夹中，创建名为 authPopup.js 的文件，并添加以下用于登录弹出窗口的身份验证和令牌获取代码：

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>重定向

在 app 文件夹中创建名为 authRedirect.js 的文件，并添加以下用于登录重定向的身份验证和令牌获取代码：

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>代码的工作原理

用户首次选择“登录”按钮时，`signIn` 方法将调用 `loginPopup` 以将用户登录。 `loginPopup` 方法会打开一个包含 Microsoft 标识平台终结点的弹出窗口，以提示输入并验证用户的凭据。 成功登录后，msal.js 将启动[授权代码流](v2-oauth2-auth-code-flow.md)。

此时，会将受 PKCE 保护的授权代码发送到受 CORS 保护的令牌终结点，并使用该代码来交换令牌。 应用程序将会收到 ID 令牌、访问令牌和刷新令牌，msal.js 将处理这些令牌，令牌中包含的信息将会被缓存。

ID 令牌包含有关用户的基本信息，例如其显示名称。 如果你计划使用 ID 令牌提供的任何数据，则后端服务器必须验证该令牌，以保证该令牌是向应用程序的有效用户颁发的。

访问令牌的生存期有限，将在 24 小时后过期。 刷新令牌可用于静默获取新的访问令牌。

在本教程中创建的 SPA 调用 `acquireTokenSilent` 和/或 `acquireTokenPopup` 来获取用于查询 Microsoft Graph API 以获取用户配置文件信息的访问令牌。 如需有关如何验证 ID 令牌的示例，请参阅 GitHub 上的 [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) 示例应用程序。 该示例使用 ASP.NET Web API 进行令牌验证。

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

初次登录后，应用不应要求用户在每次需要访问受保护资源时重新进行身份验证（即请求令牌）。 为了防止此类重新身份验证请求，请调用 `acquireTokenSilent`。 但在某些情况下，可能需要强制用户与 Microsoft 标识平台终结点交互。 例如：

- 由于密码已过期，用户可能需要重新输入其凭据。
- 应用程序正在请求访问资源，这需要用户的许可。
- 需要双重身份验证。

调用 `acquireTokenPopup` 会打开一个弹出窗口（或者 `acquireTokenRedirect` 会将用户重定向到 Microsoft 标识平台终结点）。 在该窗口中，为了进行交互，用户需要确认其凭据、为所需的资源提供许可，或者完成双重身份验证。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginPopup`（或 `loginRedirect`）后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用。 （请求或续订令牌的调用是以静默方式发出的。）`acquireTokenSilent` 在某些情况下可能会失败。 例如，用户的密码可能已过期。 应用程序可以通过两种方式处理此异常：

1. 立即调用 `acquireTokenPopup` 以触发用户登录提示。 此模式通常用于联机应用程序，此时应用程序中没有可供用户使用的未经身份验证的内容。 本指导式设置生成的示例使用此模式。
1. 直观地提示用户以交互方式登录，用户可以选择在合适的时间登录，应用程序也可以稍后重试 `acquireTokenSilent`。 如果用户可以在不中断应用程序的情况下使用应用程序的其他功能，则通常会使用此方法。 例如，应用程序中有可用的未经身份验证的内容。 在这种情况下，用户可以决定何时登录并访问受保护的资源，或何时刷新已过时的信息。

> [!NOTE]
> 本教程默认使用 `loginPopup` 和 `acquireTokenPopup` 方法。 如果使用 Internet Explorer，我们建议使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因为 Internet Explorer 和弹出窗口存在一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。 有关使用重定向方法实现相同结果的示例，请参阅 GitHub 上的 [authRedirect.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)。

## <a name="call-the-microsoft-graph-api"></a>调用 Microsoft Graph API

在 app 文件夹中创建名为 graph.js 的文件，并添加以下用于对 Microsoft Graph API 进行 REST 调用的代码：

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

在本教程创建的示例应用程序中，将使用 `callMSGraph()` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求。 然后，该请求将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 在本教程创建的示例应用程序中，受保护资源是 Microsoft Graph API me 终结点，它显示已登录用户个人资料信息。

## <a name="test-your-application"></a>测试应用程序

你已完成应用程序创建，现在可以启动 Node.js Web 服务器并测试应用的功能。

1. 从项目文件夹的根目录中运行以下命令，启动 Node.js Web 服务器：

   ```console
   npm start
   ```
1. 在浏览器中导航到 `http://localhost:3000` 或 `http://localhost:<port>`，其中，`<port>` 是 Web 服务器正在侦听的端口。 应会显示 index.html 文件的内容和“登录”按钮。

### <a name="sign-in-to-the-application"></a>登录应用程序

在浏览器加载 index.html 文件后，选择“登录”。 系统将提示你使用 Microsoft 标识平台终结点进行登录：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次登录到应用程序时，系统会提示你授予其访问你的个人资料的权限，并将你登录：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

如果你同意请求的权限，Web 应用程序会显示用户名，表示登录成功：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

### <a name="call-the-graph-api"></a>调用图形 API

登录之后，选择“查看个人资料”，以查看在调用 Microsoft Graph API 的响应中返回的用户个人资料信息：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，此作用域会自动添加到在 Azure 门户中注册的每个应用程序。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要使用 Mail.Read 作用域来列出用户的电子邮件。

添加作用域时，可能会提示用户为添加的作用域提供额外许可。

如果后端 API 不需要作用域（不建议），则可以将 `clientId` 用作调用中的作用域来获取令牌。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

如果你想要更深入了解 Microsoft 标识平台上的 JavaScript 单页应用程序开发，请参阅由多部分组成的方案系列：

> [!div class="nextstepaction"]
> [方案：单页应用程序](scenario-spa-overview.md)
