---
title: "Azure AD v2 JS SPA 设置指南 - 使用 | Microsoft Docs"
description: "JavaScript SPA 应用程序如何才能通过 Azure Active Directory v2 终结点调用需要访问令牌的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 6ff877914f1bbe0b4c4a0dfaf708f6213c4894b3
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft 身份验证库 (MSAL) 登录用户

1.  创建名为 `app.js` 的文件。 如果使用的是 Visual Studio，请选择项目（项目根文件夹），然后右键单击并选择：`Add` > `New Item` > `JavaScript File`：
2.  将以下代码添加到 `app.js` 文件：

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>更多信息

用户首次单击“调用 Microsoft 图形 API”按钮后，`callGraphApi` 方法调用 `loginRedirect` 登录用户。 此方法导致将用户重定向到 Microsoft Azure Active Directory v2 终结点，以提示和验证用户的凭据。 登录成功后，用户被重定向回原始的 index.html 页面，并接收到一个由 `msal.js` 处理的令牌，令牌中包含的信息被缓存。 该令牌称为 ID令牌，并包含有关用户的基本信息，如用户显示名。 如果计划将此令牌提供的数据用于任何目的，则需确保此令牌已由后端服务器验证，以保证将令牌颁发给应用程序的有效用户。

本指南生成的 SPA 不直接使用 ID 令牌，而是调用 `acquireTokenSilent` 和/或 `acquireTokenRedirect` 来获取用于查询 Microsoft Graph API 的访问令牌。 如果需要验证 ID 令牌的示例，请查看 GitHub 中的[此](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 示例")示例应用程序 - 该示例使用 ASP.NET Web API 进行令牌验证。

#### <a name="getting-a-user-token-interactively"></a>以交互方式获取用户令牌

初次登录后，你不希望在用户每次需要请求令牌访问资源时都要求其重新认证，因此大部分时间都应使用 acquireTokenSilent 来获取令牌。 但有些情况下，需要强制用户与 Azure Active Directory v2 终结点交互，一些示例包括：
-   由于密码已过期，用户可能需要重新输入凭据
-   应用程序正在请求访问用户需要同意的资源
-   需要双重身份验证

调用 acquireTokenRedirect(scope) 导致将用户重定向到 Azure Active Directory v2 终结点（或弹出窗口上显示 acquireTokenPopup(scope) 结果），在这种情况下，用户需要通过确认其凭证、同意所需资源，或完成双重身份验证来与之交互。

#### <a name="getting-a-user-token-silently"></a>以静默方式获取用户令牌
` acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginRedirect`（或 `loginPopup`）后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用，因为调用请求或续订令牌都以静默方式进行。
在某些情况下，`acquireTokenSilent` 可能会失败，例如用户的密码已过期。 应用程序可以通过两种方式处理此异常：

1.  立即调用 `acquireTokenRedirect`，随后出现用户登录提示。 此模式通常用于联机应用程序，此时应用程序中没有可供用户使用的未经身份验证的内容。 本指导式设置生成的示例使用此模式。

2. 应用程序还可以直观地提示用户以交互方式登录，用户可以选择在合适的时间登录，或者应用程序可以稍后重试 `acquireTokenSilent`。 如果用户可以在不中断应用程序的情况下（例如，应用程序中有可用的未经身份验证的内容）使用应用程序的其他功能，则通常会使用此方法。 在这种情况下，用户可以决定何时登录并访问受保护的资源，或何时刷新过期信息。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft Graph API

将以下代码添加到 `app.js` 文件：

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在本指南创建的示例应用程序中，使用 `callWebApiWithToken()` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求，然后将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 本指南创建的示例应用程序中的资源是 Microsoft Graph API me 终结点，它显示用户个人资料信息。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>添加方法以注销用户

将以下代码添加到 `app.js` 文件：

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
