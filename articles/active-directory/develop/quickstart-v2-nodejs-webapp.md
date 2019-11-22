---
title: Microsoft 标识平台 OIDC Node.js Web 应用快速入门 | Azure
description: 了解如何使用 OpenID Connect 在 Node.js Web 应用中实现身份验证。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ac3344fe16d24ae116a5fde289421998f11f18
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182025"
---
# <a name="quickstart-add-sign-in-using-oidc-to-a-nodejs-web-app"></a>快速入门：使用 OIDC 向 Node.js Web 应用添加登录

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

在本快速入门中，你将了解如何在使用 Node.js 和 Express 生成的 Web 应用中设置 OpenID Connect 身份验证。 该示例设计为在任何平台上运行。

## <a name="prerequisites"></a>先决条件

若要运行此示例，需要：

* 从 http://nodejs.org/ 安装 Node.js

* [Microsoft 帐户](https://www.outlook.com)或 [Office 365 开发人员计划](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>注册应用程序 
1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com/)。
1. 如果你的帐户存在于多个 Azure AD 租户中：
    - 请从页面右上角的菜单中选择你的配置文件，然后选择“切换目录”  。
    - 将会话更改为要在其中创建应用程序的 Azure AD 租户。

1. 导航到 [Azure Active Directory > 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)以注册应用。

1. 选择“新建注册”  。

1. “注册应用程序”页出现后，请输入应用的注册信息  ：
    - 在“名称”部分输入一个会显示给应用用户的有意义的名称  。 例如：MyWebApp
    - 在“支持的帐户类型”部分，选择“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)”   。

    如果有多个重定向 URI，将需要在成功创建应用后，在“身份验证”选项卡中添加这些 URI  。

1. 选择“注册”  以创建应用。

1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用   。 稍后在此项目中配置应用程序时需要此值。

1. 在应用的页面列表中，选择“身份验证”。 
    - 在“重定向 URI”部分中的组合框中选择“Web”，然后输入以下重定向 URI   ：`http://localhost:3000/auth/openid/return`
    - 在“高级设置”  部分，将“注销 URL”设置为 `http://localhost:3000`。 
    - 在“高级设置”>“隐式授权”  部分中，选中“ID 令牌”  ，因为该示例需要启用[隐式授权流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow)才能登录用户。

1. 选择“保存”。 

1. 在“证书和密码”页上的“客户端密码”部分选择“新建客户端密码”    。
    - 输入（实例应用密码的）密钥说明。
    - 选择密钥持续时间（“1 年”、“2 年”或“永不过期”）   。
    - 单击“添加”按钮时，将显示密钥值  。 将密钥值复制并保存在安全的位置。

    稍后将需要此密钥来配置应用程序。 该密钥值将不会再次显示，也无法通过其他任何方式检索，因此，该值在 Azure 门户中显示时，请立即记下来。

## <a name="download-the-sample-application-and-modules"></a>下载示例应用程序和模块

接下来，请克隆示例存储库并安装 NPM 模块。

在 shell 或命令行中：

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

或

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

在项目根目录中运行命令：

`$ npm install`  

## <a name="configure-the-application"></a>配置应用程序

按照说明在 config.js 中的 `exports.creds` 中提供参数。

* 使用格式为 \*.onmicrosoft.com. 的Azure AD 租户名称更新 `exports.identityMetadata` 中的 `<tenant_name>`。
* 使用应用注册中记录的应用程序 ID 更新 `exports.clientID`。
* 使用应用注册中记录的应用程序密码更新 `exports.clientSecret`。
* 使用应用注册中记录的重定向 URI 更新 `exports.redirectUrl`。

**生产应用的可选配置：**

* 如果要使用其他 `post_logout_redirect_uri`，请在 config.js 中更新 `exports.destroySessionUrl`。

* 如果要使用 [mongoDB](https://www.mongodb.com) 或其他[兼容的会话存储](https://github.com/expressjs/session#compatible-session-stores)，请将 config.js 中的 `exports.useMongoDBSessionStore` 设置为 true。
此示例中的默认会话存储为 `express-session`。 默认会话存储不适用于生产。

* 如果要使用 mongoDB 会话存储和其他数据库 URI，请更新 `exports.databaseUri`。

* 更新 `exports.mongoDBSessionMaxAge`。 可在此处指定要在 mongoDB 中保留会话的时间。 单位是秒。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

启动 mongoDB 服务。 如果正在此应用中使用 mongoDB 会话存储，则必须[安装 mongoDB](http://www.mongodb.org/) 并首先启动该服务。 如果使用默认会话存储，则可以跳过此步骤。

从命令行使用以下命令运行应用。

```
$ node app.js
```

**服务器输出是否难以理解？** 我们使用 `bunyan` 登录该示例。 除非你也安装了 bunyan 并按上述方式运行服务器，但将其通过 bunyan 二进制文件传递，否则控制台对你来说意义不大：

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>大功告成！

你将在 `http://localhost:3000` 上成功运行服务器。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤
详细了解 Microsoft 标识平台支持的 Web 应用方案：
> [!div class="nextstepaction"]
> [用于登录用户方案的 Web 应用](scenario-web-app-sign-user-overview.md)
