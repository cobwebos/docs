---
title: 向 Node.js Web 应用添加 OIDC 登录功能 - Microsoft 标识平台 | Azure
description: 了解如何使用 OpenID Connect 在 Node.js Web 应用程序中实现身份验证。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-javascript
ms.openlocfilehash: 149ed7aa281e50673c86c0bd7339f304aa63914a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552673"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>快速入门：向 Node.js Web 应用添加使用 OpenID Connect 登录功能

本快速入门介绍如何在使用 Node.js 和 Express 构建的 Web 应用程序中设置 OpenID Connect 身份验证。 示例设计为在任何平台上运行。

## <a name="prerequisites"></a>先决条件

若要运行此示例，需要：

* 从 http://nodejs.org/ 安装 Node.js

* [Microsoft 帐户](https://www.outlook.com)或 [Microsoft 365 开发人员计划](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>注册应用程序
1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com/)。
1. 如果你的帐户在多个 Azure AD 租户中存在：
    - 从页面右上角的菜单中选择你的个人资料，然后选择“切换目录”。
    - 将会话切换到要在其中创建应用程序的 Azure AD 租户。

1. 导航到[“Azure Active Directory”>“应用注册”](https://go.microsoft.com/fwlink/?linkid=2083908)以注册应用。

1. 选择“新建注册”。

1. “注册应用程序”页出现后，请输入应用的注册信息：
    - 在“名称”部分，输入一个要向应用用户显示的有意义的名称。 例如：MyWebApp
    - 在“支持的帐户类型”部分，选择“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)” 。

    如果有多个重定向 URI，以后需要在成功创建应用后，从“身份验证”选项卡添加这些 URI。

1. 选择“注册”以创建应用。

1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值供稍后使用。  稍后需要在此项目中使用此值来配置应用程序。

1. 在应用的页面列表中，选择“身份验证”。
    - 在“重定向 URI”部分，选择组合框中的“Web”并输入以下重定向 URI：`http://localhost:3000/auth/openid/return` 
    - 在“高级设置”部分，将“注销 URL”设置为 `https://localhost:3000`。
    - 在“高级设置”>“隐式授权”部分，选中“ID 令牌”，因为此示例需要启用“[隐式授权流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow)”才能将用户登录。 

1. 选择“保存” 。

1. 在“证书和机密”页中的“客户端机密”部分，选择“新建客户端机密”。  
    - 输入实例应用机密的密钥说明。
    - 选择密钥持续时间“1 年”、“2 年”或“永不过期”。 
    - 单击“添加”按钮时，将显示密钥值。 复制密钥值并将其保存在安全的位置。

    稍后需要使用此密钥来配置应用程序。 此密钥值将不再显示，也无法通过其他任何方式检索，因此，在 Azure 门户中显示后，请尽快记下此值。

## <a name="download-the-sample-application-and-modules"></a>下载示例应用程序和模块

接下来，克隆示例存储库并安装 NPM 模块。

从 shell 或命令行：

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

或

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

从项目根目录中运行以下命令：

`$ npm install`

## <a name="configure-the-application"></a>配置应用程序

根据说明在 config.js 的 `exports.creds` 中提供参数。

* 使用格式为 \*.onmicrosoft.com. 的Azure AD 租户名称更新 `exports.identityMetadata` 中的 `<tenant_name>`。
* 使用应用注册过程中记下的应用程序 ID 更新 `exports.clientID`。
* 使用应用注册过程中记下的应用程序机密更新 `exports.clientSecret`。
* 使用应用注册过程中记下的重定向 URI 更新 `exports.redirectUrl`。

**生产应用的可选配置：**

* 若要使用不同的 `post_logout_redirect_uri`，请更新 node.js 中的 `exports.destroySessionUrl`。

* 若要使用 [mongoDB](https://www.mongodb.com) 或其他[兼容的会话存储](https://github.com/expressjs/session#compatible-session-stores)，请将 config.js 中的 `exports.useMongoDBSessionStore` 设置为 true。
此示例中的默认会话存储为 `express-session`。 默认会话存储不适合在生产环境中使用。

* 若要使用 mongoDB 会话存储和不同的数据库 URI，请更新 `exports.databaseUri`。

* 更新 `exports.mongoDBSessionMaxAge`。 可在此处指定要将会话保留在 mongoDB 中多长时间。 单位为秒。

## <a name="build-and-run-the-application"></a>生成并运行应用程序

启动 mongoDB 服务。 如果在此应用中使用 mongoDB 会话存储，必须先[安装 mongoDB](http://www.mongodb.org/) 并启动服务。 如果使用默认会话存储，则可以跳过此步骤。

从命令行使用以下命令运行应用。

```
$ node app.js
```

**服务器输出是否难以理解？** 此示例使用 `bunyan` 进行日志记录。 除非同时安装了 bunyan 并按如上所述运行服务器，但通过管道在其中传送 bunyan 二进制文件，否则控制台没有太大作用：

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>大功告成！

将有一个服务器在 `http://localhost:3000` 上成功运行。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤
详细了解 Microsoft 标识平台支持的 Web 应用方案：
> [!div class="nextstepaction"]
> [可将用户登录的 Web 应用方案](scenario-web-app-sign-user-overview.md)
