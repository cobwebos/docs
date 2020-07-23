---
title: 教程：使用 Azure AD B2C 保护 Node.js Web API 并授予对单页应用程序 (SPA) 的访问权限
titleSuffix: Azure AD B2C
description: 本教程介绍如何使用 Active Directory B2C 保护 Node.js Web API，并从单页应用程序调用该 API。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875655"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>教程：使用 Azure AD B2C 保护 Node.js Web API 并授予从单页应用程序访问该 API 的权限

本教程介绍如何从单页应用程序调用受 Azure Active Directory B2C (Azure AD B2C) 保护的 Node.js Web API。

本教程是由两个部分组成的教程系列的第二部分，将介绍以下操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中创建 Web API 应用程序注册
> * 配置 Web API 的作用域
> * 授予 Web API 权限
> * 修改 Web API 代码示例，使其适用于你的租户

在本教程系列的[第一篇教程](tutorial-single-page-app.md)中，你已下载代码示例并已对其进行修改，以使用 Azure AD B2C 租户中的用户流将用户登录。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完成[教程：使用 Azure AD B2C 在单页应用程序中启用身份验证](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) 或其他代码编辑器
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>添加 Web API 应用程序

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>配置作用域

可通过作用域控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，你将定义 Web API 的读取和写入权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

记下“作用域”下的 `demo.read` 作用域值，因为稍后在配置单页应用程序的步骤中需要使用该值。  完整作用域值类似于 `https://contosob2c.onmicrosoft.com/api/demo.read`。

## <a name="grant-permissions"></a>授予权限

若要从另一应用程序调用受保护的 Web API，需授予应用程序访问该 Web API 的权限。

在先决条件教程中，你已创建名为 *webapp1* 的 Web 应用程序。 在本教程中，你要将该应用程序配置为调用在上一部分创建的 Web API：*webapi1*。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

现在，已经为单页 Web 应用程序授予了在指定作用域内对受保护 Web API 的权限。 用户通过 Azure AD B2C 进行身份验证，以使用单页应用程序。 该单页应用使用授权流通过 Azure AD B2C 返回的访问令牌访问受保护的 Web API。

## <a name="configure-the-sample"></a>配置示例

注册 Web API 并定义作用域后，请配置 Web API 代码，使之适用于你的 Azure AD B2C 租户。 在本教程中，你将配置一个可从 GitHub 下载的示例 Node.js Web API。

[下载 \*.zip 存档](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)，或者从 GitHub 克隆示例 Web API 项目。 还可以直接浏览到 GitHub 上的 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 项目。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>配置 Web API

1. 在代码编辑器中打开 config.js 文件。 
1. 修改变量值，以反映前面创建的应用程序注册的值。 另外，使用作为先决条件的一部分创建的用户流更新 `policyName`。 例如 B2C_1_signupsignin1。 

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>启用 CORS

若要允许单页应用程序调用 Node.js Web API，需要在该 Web API 中启用 [CORS](https://expressjs.com/en/resources/middleware/cors.html)。 在生产应用程序中，应密切关注哪个域正在发出请求，但对于本教程，可允许来自任何域的请求。

若要启用 CORS，请使用以下中间件。 在本教程所述的 Node.js Web API 代码示例中，此中间件已添加到 index.js 文件。 

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>配置单页应用程序

本教程系列的[前一篇教程](tutorial-single-page-app.md)中所述的单页应用程序 (SPA) 使用 Azure AD B2C 进行用户注册和登录，并且默认会调用 fabrikamb2c 演示租户保护的 Node.js Web API。 

在本部分，你将更新该单页 Web 应用程序，以调用你的 Azure AD B2C 租户保护的 Node.js Web API（在本地计算机上运行）。 

若要更改 SPA 中的设置：

1. 在学习上一篇教程时下载或克隆的 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 项目中，打开 JavaScriptSPA 文件夹中的 apiConfig.js 文件。  
1. 使用前面创建的 *demo.read* 作用域的 URI 以及 Web API 的 URL 配置示例。
    1. 在 `apiConfig` 定义中，将 `b2cScopes` 值替换为 demo.read 作用域（前面记下的“作用域”值）的完整 URI。  
    1. 将 `webApi` 值中的域更改为在前面的步骤中注册 Web API 应用程序时所添加的重定向 URI。

    由于可在 `/hello` 终结点上访问该 API，因此请保留 URI 中的 /hello。 

    `apiConfig` 定义应类似于以下代码块，不过，应将其中的 `<your-tenant-name>` 替换为你的 B2C 租户名称：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>运行 SPA 和 Web API

现已准备好测试单页应用程序在限定作用域内对 API 的访问。 在本地计算机上同时运行 Node.js Web API 和示例 JavaScript 单页应用程序。 然后登录到该单页应用程序，并选择“调用 API”按钮向受保护的 API 发起请求。 

尽管在本教程中这两个应用程序都在本地运行，但你已将它们配置使用 Azure AD B2C 进行安全注册/登录并授予对受保护 Web API 的访问权限。

### <a name="run-the-nodejs-web-api"></a>运行 Node.js Web API

1. 打开控制台窗口，切换到包含 Node.js Web API 示例的目录。 例如：

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 运行以下命令：

    ```console
    npm install && npm update
    node index.js
    ```

    控制台窗口将显示托管该应用程序的端口号。

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>运行单页应用

1. 打开另一个控制台窗口，并切换到包含 JavaScript SPA 示例的目录。 例如：

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. 运行以下命令：

    ```console
    npm install && npm update
    npm start
    ```

    控制台窗口将显示该应用程序所在的端口号。

    ```console
    Listening on port 6420...
    ```

1. 在浏览器中导航到 `http://localhost:6420`，查看此应用程序。

    ![浏览器中显示的单页应用程序示例应用](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. 使用在[上一篇教程](tutorial-single-page-app.md)中所用的电子邮件地址和密码登录。 成功登录后，应会看到 `User 'Your Username' logged-in` 消息。
1. 选择“调用 API”  按钮。 SPA 将获取 Azure AD B2C 授予的授权，然后访问受保护的 Web API 以显示登录用户的名称：

    ![浏览器中的单页应用程序，其中显示了 API 返回的用户名 JSON 结果](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已完成以下操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中创建了 Web API 应用程序注册
> * 配置了 Web API 的作用域
> * 授予了对 Web API 的权限
> * 修改了 Web API 代码示例，使其适用于你的租户

了解 SPA 如何从受保护的 Web API 请求资源后，接下来可以更深入地了解这些应用程序类型如何彼此交互以及与 Azure AD B2C 交互。

> [!div class="nextstepaction"]
> [可在 Azure Active Directory B2C 中使用的应用程序类型 >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
