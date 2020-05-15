---
title: JavaScript 单页应用教程 2.0 - Microsoft 标识平台 | Azure
description: JavaScript SPA 应用程序如何通过 Azure Active Directory v2.0 终结点使用授权代码流调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890380"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>让用户登录并从 JavaScript 单页应用程序 (SPA) 调用 Microsoft Graph API - MSAL.js 2.0

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会更改。

本教程使用某个版本的 MSAL.js，该版本将 OAuth 2.0 授权代码流与 PKCE 配合使用。 若要详细了解此协议以及隐式流与授权代码流之间的差异，请参阅此[文档](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)。 如果你要寻找使用隐式流的教程，请参阅 [MSAL.js v1 教程](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa)。

此 MSAL.js 版本改进了当前的 msal-core 库，并在浏览器中使用授权代码流。 旧库中提供的大多数功能在此版本中也有提供，但两者中的身份验证流存在细微的差别。 此版本不支持隐式流  。

本指南演示 JavaScript 单页应用程序 (SPA) 如何执行以下操作：
- 将个人帐户以及工作和学校帐户登录
- 获取访问令牌
- 从 Microsoft 标识平台终结点调用需要访问令牌的 Microsoft Graph API 或其他 API 

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![显示本教程生成的示例应用的工作原理](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>详细信息

本指南创建的示例应用程序允许 JavaScript SPA 查询从 Microsoft 标识平台终结点接受令牌的 Microsoft 图形 API 或 Web API。 在此方案中，用户登录后请求了访问令牌，并通过授权标头将其添加到 HTTP 请求。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。

### <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|“适用于 JavaScript 的 Microsoft 身份验证库”浏览器包|

## <a name="set-up-your-web-server-or-project"></a>设置 Web 服务器或项目

想要改为下载此示例的项目？ 若要使用本地 Web 服务器（如 Node.js）运行项目，请克隆项目文件：

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

若要在执行代码示例之前对其进行配置，请跳到[配置步骤](#register-your-application)。

## <a name="prerequisites"></a>先决条件

* 若要运行本教程，需要一个本地 Web 服务器，例如 [Node.js](https://nodejs.org/en/download/) 或 [.NET Core](https://www.microsoft.com/net/core)。

* 如果使用 Node.js 来运行项目，请安装集成开发环境 (IDE)（如 [Visual Studio Code](https://code.visualstudio.com/download)）以编辑项目文件。

* 本教程中的说明基于 Node.js。

## <a name="create-your-project"></a>创建项目

确保已安装 [Node.js](https://nodejs.org/en/download/)，然后创建一个用于托管应用程序的文件夹。 接下来，实现一个小型 [Express](https://expressjs.com/) Web 服务器来为 `index.html` 文件提供服务。

1. 首先，在终端中导航到你的项目文件夹，然后运行以下 NPM 命令。
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. 接下来，创建名为 server.js 的 .js 文件并添加以下代码  ：

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

现在，已有一个可为 SPA 提供服务的简单服务器。 在本教程结束时，所需的文件夹结构如下：

![所需 SPA 文件夹结构的文本说明](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>创建 SPA UI

1. 在 app 文件夹中为 JavaScript SPA 创建 index.html 文件   。 此文件实现通过 Bootstrap 4 Framework 生成的 UI，并导入用于配置、身份验证和 API 调用的脚本文件  。

   在 *index.html* 文件中，添加以下代码：

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
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
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
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

   > [!TIP]
   > 可以将上述脚本中的 MSAL.js 版本替换为 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)下的最新发布版本。


2. 现在，创建名为 ui.js 的、用于访问和更新 DOM 元素的 .js 文件，并添加以下代码  ：

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

按照说明[注册新的单页应用程序](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)。

#### <a name="set-a-redirect-url-for-nodejs"></a>设置 Node.js 的重定向 URL

对于 Node.js，可以在 *server.js* 文件中设置 Web 服务器端口。 本教程使用端口 3000，但你可以使用任何其他可用端口。

若要在应用程序注册信息中设置重定向 URL，请切换回“应用程序注册”  窗格，然后执行以下操作之一注册新的 SPA  ：

- 将 *`http://localhost:3000/`* 设置为“重定向 URL”  。
- 如果使用的是自定义 TCP 端口，请使用 *`http://localhost:<port>/`* （其中， *\<端口>* 是自定义 TCP 端口号）。

### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

创建名为 authConfig.js 的、包含用于身份验证的配置参数的新 .js 文件，并添加以下代码  ：

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 其中：
 - *\<Enter_the_Application_Id_Here>* 是已注册的应用程序的“应用程序(客户端) ID”  。
 - *\<Enter_the_Cloud_Instance_Id_Here>* 是 Azure 云的实例。 对于主要或全球 Azure 云，只需输入 *https://login.microsoftonline.com* 。 对于**国家**云（例如“中国”云），请参阅[国家云](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)。
 - *\<Enter_the_Tenant_info_here>* 设置为以下选项之一：
   - 如果应用程序支持“此组织目录中的帐户”  ，请将此值替换为“租户 ID”  或“租户名称”  （例如，*contoso.microsoft.com*）。
   - 如果应用程序支持“任何组织目录中的帐户”，请将此值替换为 **organizations**  。
   - 如果应用程序支持“任何组织目录中的帐户和个人 Microsoft 帐户”  ，请将此值替换为 **common**。 若要限制对“仅限个人 Microsoft 帐户”  的支持，请将此值替换为 **consumers**。
- \Enter_the_Redirect_Uri_Here > 是你在门户中注册的端口 (`http://localhost:3000/`)  


创建名为 `graphConfig.js` 的、包含用于调用 Microsoft Graph API 的配置参数的新 .js 文件，并添加以下代码：
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- *\<Enter_the_Graph_Endpoint_Here>* 是 MS Graph API 的实例。 对于全局 MS Graph API 终结点，只需将此字符串替换为 `https://graph.microsoft.com` 即可。 对于国家云部署，请参阅 [Graph API 文档](https://docs.microsoft.com/graph/deployments)。

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft 身份验证库 (MSAL) 登录用户

### <a name="popup"></a>弹出窗口
创建名为 `authPopup.js` 的、包含登录弹出窗口的身份验证和令牌获取逻辑的新 .js 文件，并添加以下代码：

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>重定向
创建名为 `authRedirect.js` 的、包含登录重定向的身份验证和令牌获取逻辑的新 .js 文件，并添加以下代码：

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>详细信息

用户首次选择“登录”按钮后，`signIn` 方法将调用 `loginPopup` 以将用户登录。  此方法会打开一个包含 Microsoft 标识平台终结点的弹出窗口，以提示并验证用户的凭据。  成功登录后，msal.js 将启动[授权代码流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)  。

此时，会将受 PKCE 保护的授权代码发送到受 CORS 保护的令牌终结点，并使用该代码来交换令牌。 将会收到 ID 令牌、访问令牌和刷新令牌，msal.js 将处理这些令牌，令牌中包含的信息将会被缓存  。

ID 令牌包含有关用户的基本信息，例如其显示名称。 如果你打算使用此令牌提供的任何数据，则后端服务器必须验证该令牌，以保证该令牌是向应用程序的有效用户颁发的。 刷新令牌的生存期有限，将在 24 小时后过期。 刷新令牌可用于静默获取新的访问令牌。

本指南生成的 SPA 调用 `acquireTokenSilent` 和/或 `acquireTokenPopup` 来获取用于查询 Microsoft Graph API 以获取用户配置文件信息的访问令牌  。 如需有关如何验证 ID 令牌的示例，请参阅 GitHub 上的 [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) 示例应用程序。 该示例使用 ASP.NET Web API 进行令牌验证。

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

首次登录后，系统不应该要求用户在每次需要请求令牌来访问资源时，都要重新进行身份验证。 为了防止此类重新身份验证请求，请使用 `acquireTokenSilent`。 但在某些情况下，可能需要强制用户与 Microsoft 标识平台终结点交互。 例如：

- 由于密码已过期，用户可能需要重新输入其凭据。
- 应用程序正在请求访问资源，这需要用户的许可。
- 需要双重身份验证。

调用 `acquireTokenPopup` 会打开一个弹出窗口（或者 `acquireTokenRedirect` 会将用户重定向到 Microsoft 标识平台终结点）。 在该窗口中，为了进行交互，用户需要确认其凭据、为所需的资源提供许可，或者完成双重身份验证。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginPopup`（或 `loginRedirect`）后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用。 （请求或续订令牌的调用是以静默方式发出的。）`acquireTokenSilent` 在某些情况下可能会失败。 例如，用户的密码可能已过期。 应用程序可以通过两种方式处理此异常：

1. 立即调用 `acquireTokenPopup` 以触发用户登录提示。 此模式通常用于联机应用程序，此时应用程序中没有可供用户使用的未经身份验证的内容。 本指导式设置生成的示例使用此模式。

1. 直观地提示用户以交互方式登录，用户可以选择在合适的时间登录，应用程序也可以稍后重试 `acquireTokenSilent`。 如果用户可以在不中断应用程序的情况下使用应用程序的其他功能，则通常会使用此方法。 例如，应用程序中有可用的未经身份验证的内容。 在这种情况下，用户可以决定何时登录并访问受保护的资源，或何时刷新已过时的信息。

> [!NOTE]
> 本快速入门默认使用 `loginPopup` 和 `acquireTokenPopup` 方法。 如果使用 Internet Explorer 作为浏览器，我们建议使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因为 Internet Explorer 处理弹出窗口的方式存在一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。 若要了解如何使用重定向方法实现相同的结果，请参阅 [authRedirect.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)  。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>使用刚刚获取的令牌调用 Microsoft Graph API


 创建名为 graph.js 的、用于对 Microsoft Graph API 发出 REST 调用的 .js 文件，并添加以下代码  ：

   ```javascript

// Helper function to call MS Graph API endpoint
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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在本指南创建的示例应用程序中，将使用 `callMSGraph()` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求。 然后，该请求将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌  。 本指南创建的示例应用程序中的资源是 Microsoft Graph API *me* 终结点，它显示用户个人资料信息。

## <a name="test-your-code"></a>测试代码

1. 对于 Node.js，请从应用程序文件夹中运行以下命令来启动 Web 服务器：

   ```bash
   npm install
   npm start
   ```
1. 在浏览器中输入 **http://localhost:3000** 或 **http://localhost:{port}** ，其中，port 是 Web 服务器正在侦听的端口。  应会显示 index.html 文件的内容和“登录”按钮   。

## <a name="test-your-application"></a>测试应用程序

在浏览器加载 index.html 文件后，选择“登录”。   系统将提示你使用 Microsoft 标识平台终结点进行登录：

![JavaScript SPA 帐户登录窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次登录到应用程序时，系统会提示你授予其访问你的个人资料的权限，并将你登录：

![“请求的权限”窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>查看应用程序结果

登录后，你的用户个人资料信息将在显示的 Microsoft Graph API 响应中返回：

![Microsoft Graph API 调用的结果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，此作用域会自动添加到在 Azure 门户中注册的每个应用程序。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要使用 Mail.Read 作用域来列出用户的邮件  。

> [!NOTE]
> 当你添加作用域时，系统可能会提示用户另外授权。

如果后端 API 不需要范围（不建议），则你可以将 *clientId* 用作调用中的范围来获取令牌。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

[MSAL.js GitHub 存储库](https://github.com/AzureAD/microsoft-authentication-library-for-js)包含其他库文档、常见问题解答，并提供问题支持。
