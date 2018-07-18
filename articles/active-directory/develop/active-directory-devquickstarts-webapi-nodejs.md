---
title: Azure AD Node.js Web API 入门 | Microsoft Docs
description: 如何生成一个与 Azure AD 集成、可用于身份验证的 Node.js REST Web API。
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 24591c46858970724fbd1fe36336f7f2b0b2fefd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155831"
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD Node.js Web API 入门

本文演示如何使用 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) 模块通过 [Passport](http://passportjs.org/) 保护 [Restify](http://restify.com/) API 终结点，以处理与 Azure Active Directory (AAD) 之间的通信。 

本教程的范围包括有关保护 API 终结点的注意事项。 本文未涉及有关登录和保留身份验证令牌的注意事项，这些问题由客户端应用程序负责。 有关客户端实现的详细信息，请查看[使用 Azure AD 进行 Node.js Web 应用登录和注销](active-directory-devquickstarts-openidconnect-nodejs.md)。

[GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic) 中提供了与本文相关的完整代码示例。

## <a name="create-the-sample-project"></a>创建示例项目
此服务器应用程序需要安装几个包依赖项才能支持 Restify 和 Passport，以及传递给 AAD 的帐户信息。

若要开始，请将以下代码添加到名为 `package.json` 的文件中：

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

创建 `package.json` 后，在命令提示符下运行 `npm install` 来安装包依赖项。 

### <a name="configure-the-project-to-use-active-directory"></a>将项目配置为使用 Active Directory

若要开始配置应用程序，可以通过 Azure CLI 获取几个特定于帐户的值。 CLI 的最简单入门方法是使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在 Cloud Shell 中输入以下命令： 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

`create` 命令的[参数](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create)包括：

| 参数  | 说明 |
|---------|---------|
|`display-name` | 注册的友好名称 |
|`homepage` | 用户可在其中登录和使用应用程序的 URL |
|`identifier-uris` | 空格分隔的唯一 URI，Azure AD 可将其用于此应用程序 |

在连接到 Azure Active Directory 之前，需准备好以下信息：

| 名称  | 说明 | 配置文件中的变量名称 |
| ------------- | ------------- | ------------- |
| 租户名称  | 要用于身份验证的[租户名称](active-directory-howto-tenant.md) | `tenantName`  |
| 客户端 ID  | 客户端 ID 是对 AAD _应用程序 ID_ 使用的 OAuth 术语。 |  `clientID`  |

从 Azure Cloud Shell 的注册响应中，复制 `appId` 值并创建名为 `config.js` 的新文件。 接下来，添加以下代码，并将带有括号的标记替换为自己的值：

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
有关各项配置设置的详细信息，请查看 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) 模块文档。

## <a name="implement-the-server"></a>实现服务器
[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) 模块附带两种身份验证策略：[OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) 和[持有者](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy)策略。 本文中实现的服务器使用“持有者”策略来保护 API 终结点。

### <a name="step-1-import-dependencies"></a>步骤 1：导入依赖项
创建名为 `app.js` 的新文件并在其中粘贴以下文本：

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

在此代码片段中：

- 引用了 `restify` 和 `restify-plugins` 模块以设置 Restify 服务器。

- `passport` 和 `passport-azure-ad` 模块负责与 AAD 通信。

- `config` 变量已使用在前一步骤中创建的 `config.js` 文件中的值初始化。

- 为 `authenticatedUserTokens` 创建了一个数组，将用户令牌传递到受保护的终结点时，该数组会存储这些令牌。

- `serverPort` 是从进程环境的端口或配置文件定义的。

### <a name="step-2-instantiate-an-authentication-strategy"></a>步骤 2：实例化身份验证策略
保护终结点时，必须提供一个策略来负责确定当前请求是否源自经过身份验证的用户。 此处的 `authenticatonStrategy` 变量是 `passport-azure-ad` `BearerStrategy` 类的实例。 在 `require` 语句的后面添加以下代码。

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
此实现通过将身份验证令牌添加到 `authenticatedUserTokens` 数组（如果这些令牌不存在）来使用自动注册。

创建策略的新实例后，必须通过 `use` 方法将它传递给 Passport。 将以下代码添加到 `app.js`，以便在 Passport 中使用该策略。

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>步骤 3：服务器配置
定义身份验证策略后，可在 Restify 服务器中指定一些基本设置，并将其设置为使用 Passport 来获得安全性。

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
将此服务器初始化并将其配置为分析授权标头，然后将其设置为使用 Passport。


### <a name="step-4-define-routes"></a>步骤 4：定义路由
现在可以定义路由，并确定要使用 AAD 保护哪个路由。 此项目包含两个路由，其中，根级别是开放的，`/api` 路由设置为要求身份验证。

在 `app.js` 中，为根级别路由添加以下代码：

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

根路由允许所有请求通过，并返回一条消息，其中包含用于测试 `/api` 路由的命令。 相比之下，`/api` 路由已使用 [`passport.authenticate`](http://passportjs.org/docs/authenticate) 锁定。 在根路由的后面添加以下代码。

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

此配置仅允许包含持有者令牌的、经过身份验证的请求访问 `/api`。 `session: false` 选项用于禁用会话，要求在对 API 发出的每个请求中传递令牌。

最后，通过调用`listen` 方法将服务器设置为在配置的端口上侦听。

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>运行示例

实现服务器后，可以通过打开命令提示符并输入以下命令来启动服务器：

```Shell
npm start
```

运行服务器后，可将一个请求提交到服务器以测试结果。 若要演示根路由返回的响应，请打开 bash shell 并输入以下代码：

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

如果已正确配置服务器，则响应应如下所示：

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

接下来，可在 bash shell 中输入以下命令，测试要求身份验证的路由：

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

如果已正确配置服务器，则服务器应在响应中返回 `Unauthorized` 状态。

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
创建安全 API 后，可以实现一个能够向 API 传递身份验证令牌的客户端。

## <a name="next-steps"></a>后续步骤
如简介中所述，必须实现一个客户端对等组件，才能连接到可处理登录、注销和管理令牌的服务器。 有关基于代码的示例，可以参考 [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) 和 [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android) 中的客户端应用程序。 有关分步教程，请参考以下文章：

> [!div class="nextstepaction"]
> [使用 Azure AD 进行 Node.js Web 应用登录和注销](active-directory-devquickstarts-openidconnect-nodejs.md)